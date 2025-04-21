
# Langkah-langkah Konfigurasi Jaringan VM1 dan VM2

## 1. Pastikan Forwarding IP Aktif di VM1
VM1 harus meneruskan lalu lintas dari VM2 ke internet. Pastikan forwarding IP diaktifkan dengan menjalankan:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

Agar permanen, tambahkan di `/etc/sysctl.conf`:

```bash
net.ipv4.ip_forward=1
```

Lalu jalankan:

```bash
sysctl -p
```

## 2. Atur NAT di VM1 (iptables)
Karena VM1 memiliki dua interface (enp0s3 ke internet dan enp0s8 ke VM2), Anda perlu menerapkan NAT agar VM2 bisa keluar melalui VM1.

Jalankan perintah berikut di VM1:

```bash
iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
iptables -A FORWARD -i enp0s3 -o enp0s8 -m state --state RELATED,ESTABLISHED -j ACCEPT
```

Agar iptables tetap berlaku setelah reboot, jalankan:

```bash
apt install iptables-persistent
netfilter-persistent save
netfilter-persistent reload
```

## 3. Atur Gateway di VM2
Di VM2, atur gateway agar menggunakan VM1 dengan perintah:

```bash
ip route add default via 192.168.200.1
```

Pastikan di `/etc/network/interfaces` atau `/etc/netplan/` (jika menggunakan netplan) konfigurasi gatewaynya benar.

## 4. Pastikan DNS di VM2 Mengarah ke VM1
Edit `/etc/resolv.conf` di VM2:

```bash
nameserver 192.168.200.1
```

Jika menggunakan `systemd-resolved`, bisa diatur di `/etc/systemd/resolved.conf`:

```ini
[Resolve]
DNS=192.168.200.1
FallbackDNS=8.8.8.8
```

Lalu restart service:

```bash
systemctl restart systemd-resolved
```

## 5. Konfigurasi BIND9 di VM1 (Opsional)
Jika VM1 bertindak sebagai DNS Server untuk VM2, pastikan konfigurasi di `/etc/bind/named.conf.options`:

```bash
options {
    directory "/var/cache/bind";

    recursion yes;
    allow-query { any; };
    forwarders {
        8.8.8.8;
        1.1.1.1;
    };
};
```

Lalu restart BIND:

```bash
systemctl restart bind9
```

Setelah ini, uji koneksi dari VM2:

```bash
ping 8.8.8.8   # Cek apakah bisa terhubung ke internet
ping google.com # Cek apakah DNS berfungsi
```

