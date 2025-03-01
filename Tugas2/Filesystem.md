# Filesystem
Filesystem mengatur dan mengelola sumber daya penyimpanan. Terdiri dari empat komponen utama:
- **Namespace**: Sistem penamaan hierarkis
- **API**: Panggilan sistem untuk navigasi dan manipulasi berkas
- **Security Models**: Mekanisme kontrol akses
- **Implementation**: Perangkat lunak yang menghubungkan model logis ke perangkat keras

Sistem berkas berbasis disk yang umum digunakan:
- **ext4, XFS, UFS, ZFS, Btrfs**
- **FAT, NTFS** (Windows) dan **ISO 9660** (CD/DVD)

# Pathnames
Menentukan lokasi berkas dalam hierarki (absolut: `/home/user/file.txt`, relatif: `./file.txt`)

# Filesystem Mounting and Unmounting
`mount` untuk memasang, `umount` untuk melepas sistem berkas
```sh
# Mount the filesystem on /dev/sda4 to /users
mount /dev/sda4 /users
```
Pelepasan paksa: `umount -f` (paksa), `umount -l` (malas)
```sh
# Find out which processes are using the filesystem
abdou@debian:~$ lsof /home/abdou
```
Menyelidiki proses yang menggunakan sistem berkas: `ps`
```sh
# Investigate the processes that are using the filesystem
abdou@debian:~$ ps up "1234 5678 91011"
```

# Organization of the file tree
Sistem UNIX memiliki struktur kompleks dengan direktori utama:
- **Root (`/`)**: Menampung direktori utama sistem
- **`/boot`**: Berkas kernel
- **`/etc`**: Berkas konfigurasi sistem
- **`/sbin`, `/bin`**: Utilitas penting
- **`/dev`**: Berkas perangkat (sistem berkas virtual)
- **`/lib`, `/lib64`**: Pustaka bersama
- **`/usr`**: Program standar dan manual
- **`/var`**: Log, antrian cetak, data yang berubah

# File Types 
1. **Regular File**: Teks, data, eksekutabel
2. **Directories**: Berisi berkas lainnya
3. **Character devices files**: Komunikasi dengan perangkat keras
4. **Block devices files**: Antarmuka perangkat penyimpanan
5. **Local domain sockets**: Komunikasi antar-proses
6. **Named pipes(FIFOs)**: Komunikasi antar-proses
7. **Symbolic links**: Alias untuk berkas

# File Attributes
- **Permission bits**:  
    Baca, tulis, eksekusi (`rwx` untuk pemilik, grup, lainnya)
  ```sh
  #!/usr/bin/perl
  ```
- **The setuid and setgid bits**
  - **SetUID (`4000`)**: Menjalankan berkas sebagai pemilik.
  - **SetGID (`2000`)**: Mempertahankan kepemilikan grup.
  - **Sticky Bit (`1000`)**: Membatasi penghapusan berkas oleh pengguna lain.
- **Cek izin**
  ```sh
  $ ls -l /dev/tty0
  crw--w---- 1 root tty 4, 0 Mar  1  2020 /dev/tty0
  ```
- **chmod: change permissions**:  
    Mengubah mode suatu berkas
  ```sh
  chmod 755 namaberkas
  ```
- **chown: change ownership**:  
    Mengubah pemilik dan grup file. Opsi -R menyebabkan chown mengubah kepemilikan konten file secara rekursif
  ```sh
  $ chown -R abdou:users /home/abdou
  ```
- **chgrp: change group**:  
    Mengubah grup suatu file. Opsi -R menyebabkan chown mengubah kepemilikan konten file secara rekursif
  ```sh
  $ chgrp -R users /home/abdou
  ```
- **umask: set default permissions**:  
    Menetapkan izin default untuk file dan direktori baru. Perintah umask adalah bit mask yang dikurangi dari izin default untuk menentukan izin sebenarnya
  ```sh
  $ umask 022
  ```

# Access Control Lists (ACLs)
ACL memperluas model izin Unix tradisional:
- **Menampilkan suatu berkas**:
  ```sh
  $ getfacl /etc/passwd
  ```
- **Menetapkan suatu berkas**:
  ```sh
  $ setfacl -m u:abdou:rw /etc/passwd
  ```
- **Implementation of ACLs**:  
    Secara teori, tanggung jawab untuk memelihara dan menegakkan ACLs dapat diberikan kepada beberapa komponen sistem yang berbeda. ACLs dapat diimplementasikan oleh kernel atas nama semua sistem berkas sistem, oleh sistem berkas individual, atau mungkin oleh perangkat lunak tingkat tinggi seperti server NFS dan SMB
- **POSIX ACLs**:  
    ACLs POSIX adalah ACLs Unix tradisional. ACLs ini didukung oleh sebagian besar sistem operasi mirip Unix, termasuk Linux, FreeBSD, dan Solaris
  ```sh
  $ setfacl -m user:abdou:rwx,group:users:rwx,other::r /home/abdou
  $ getfacl --omit-header /home/abdou
   ```
- **NFSv4 ACLs**:  
    ACLs NFSv4 adalah jenis ACLs yang lebih baru dan lebih canggih. ACLs ini didukung oleh beberapa sistem operasi mirip Unix, termasuk Linux dan FreeBSD.
  
    ACLs NFSv4 mirip dengan ACLs POSIX, tetapi memiliki beberapa fitur tambahan. Misalnya, ACLs NFSv4 memiliki ACLs default yang digunakan untuk mengatur ACLs file dan direktori baru

  



