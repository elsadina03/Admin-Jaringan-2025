# Process Control

## Components of a Process

Proses terdiri dari ruang alamat dan struktur data kernel. Ruang alamat menyimpan kode, data, dan tumpukan proses

### The PID: Process ID Number
Setiap proses memiliki nomor unik yang digunakan untuk identifikasi
```sh
ps -e
```

### The PPID: Parent Process ID Number
PPID adalah ID dari proses induk yang menciptakan proses tersebut
```sh
ps -o pid,ppid,cmd
```

### The UID and EUID: User ID and Effective User ID
UID adalah ID pengguna yang menjalankan proses, sedangkan EUID menentukan hak akses proses
```sh
id
```

## Lifecycle of a Process
Proses dibuat dengan `fork()`, dan bisa dieksekusi atau dihentikan
```sh
ps -ef | grep bash
```

## Signals
Sinyal adalah cara untuk mengirim pemberitahuan ke suatu proses. Contoh sinyal:

- **KILL**: Menghentikan proses secara paksa
  ```sh
  kill -TERM <PID>
  ```
- **INT**: Menghentikan proses dengan kombinasi keyboard
  ```sh
  Ctrl + C
  ```
- **TERM**: Mengakhiri proses secara normal
  ```sh
  kill -15 <PID>
  ```
- **HUP**: Memutus terminal atau me-restart daemon
  ```sh
  kill -1 <PID>
  ```
- **QUIT**: Menghentikan proses dan menghasilkan core dump
  ```sh
  kill -3 <PID>
  ```

## PS: Proses Pemantauan
`ps` digunakan untuk memantau proses sistem
```sh
ps aux
```

## Interactive Monitoring with `top` and `htop`
`top` menampilkan proses secara real-time
```sh
top
```
`htop` adalah versi `top` dengan tampilan lebih baik
```sh
htop
```

## Nice and Renice: Changing Process Priority
`nice` menentukan prioritas saat menjalankan proses baru, sedangkan `renice` mengubah prioritas proses yang sudah berjalan
```sh
nice -n 10 ./script.sh
```

## The `/proc` Filesystem
`/proc` adalah sistem berkas semu yang menyimpan informasi status sistem
```sh
cat /proc/cpuinfo
```

## `strace` and `truss`
`strace` (Linux) dan `truss` (FreeBSD) digunakan untuk men-debug program dengan melacak panggilan sistem
```sh
strace -p 1234
```

## Runaway Processes
Proses yang menghabiskan 100% CPU dapat dihentikan dengan `kill`
```sh
kill -9 1234
```

## Periodic Processes
`cron` digunakan untuk menjadwalkan tugas berulang, konfigurasi cron disebut `crontab`
```sh
*/30 * * * * /path/to/script.sh
```

## Systemd Timer
Alternatif `cron`, lebih fleksibel dengan konfigurasi unit `.timer`
```sh
systemctl list-timers
```

## Common Use for Scheduled Tasks

### Sending Mail
Mengirim email laporan harian atau hasil eksekusi perintah
```sh
echo "Laporan harian" | mail -s "Subject" user@example.com
```

### Cleaning Up a Filesystem
Menghapus file lama dalam direktori sampah
```sh
0 0 * * * /usr/bin/find /home/abdou/.local/share/Trash/files -mtime +30 -exec /bin/rm -f {} \;
```

### Rotating a Log File
Memutar berkas log untuk membaginya menjadi beberapa segmen
```sh
logrotate /etc/logrotate.conf
```

### Running Batch Jobs
Menjalankan pekerjaan batch untuk pemrosesan data besar
```sh
bash process_queue.sh
```

### Backing Up and Mirroring
Membuat salinan sistem atau direktori secara berkala.
```sh
rsync -av /data /backup/

