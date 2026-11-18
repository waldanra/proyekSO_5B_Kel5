# Portofolio Proyek Akhir Sistem Operasi

## 1. Identitas Mahasiswa

| Keterangan | Detail |
| :--- | :--- |
| **Nama Lengkap** | Waldan Nur Rabbani |
| **NIM** | 2510512179 |
| **Kelas / Kelompok** | Kelas E / Kelompok 5 |
| **Judul Proyek** | Pilihan 5B — Sistem Antrean Pekerjaan dan Monitoring Proses Linux (`proyek-so`) |
| **Peran Utama** | Pengujian Keamanan Akses, Isolasi Kontainer Docker, dan Dokumentasi Desain Sistem |

---

## 2. Asal Proyek

Proyek ini merupakan hasil kerja kolaboratif tim **Kelompok 5 (Kelas 5B)** pada mata kuliah Sistem Operasi dengan judul **Pilihan 5B — Sistem Antrean Pekerjaan dan Monitoring Proses Linux (`proyek-so`)**. 

> **Catatan Kejujuran Akademik:** 
> Seluruh arsitektur inti, skrip utama antrean pekerjaan, maupun komponen pemantauan proses dalam repositori ini adalah **karya bersama kelompok dan bukan klaim karya pribadi sepenuhnya**. Portofolio ini disusun secara khusus untuk mendokumentasikan kontribusi spesifik, pengujian keamanan, analisis teknis, dan pembelajaran individu saya sebagai bagian dari tim.

* **Repository Utama Kelompok:** [https://github.com/iiqbalitoo/proyekSO_5B_Kel5](https://github.com/iiqbalitoo/proyekSO_5B_Kel5)
* **Daftar Anggota Kelompok 5:**
  1. Muhammad Iqbal Rizqullah
  2. Harsya Maulana Syahbani
  3. Ananda Hafiz Aulia
  4. Muhammad Fathir Natadiwangsa
  5. Rifqi Naufal Anwar
  6. **Waldan Nur Rabbani** *(Saya)*

---

## 3. Kontribusi Pribadi

Dalam pengerjaan proyek akhir ini, kontribusi spesifik yang saya kerjakan meliputi aspek pengujian keamanan sistem, isolasi kontainer, dan penyusunan dokumentasi teknis:

1. **Pengujian & Dokumentasi Pembatasan Akses (User/Group):** Menguji mekanisme hak akses direktori dan berkas sistem antrean menggunakan akun pengguna di luar grup yang berwenang (akun `tamu`) untuk memastikan isolasi akses berjalan sesuai rancangan.
2. **Pengujian & Dokumentasi Docker Bind Mount (`read-only`):** Melakukan simulasi isolasi pembacaan hasil laporan sistem menggunakan kontainer ringan `alpine:3.20` dengan mode *read-only* guna mencegah modifikasi data dari dalam kontainer.
3. **Penyusunan Laporan Bab 2 (Desain Sistem & Spesifikasi):** Menyusun rancangan arsitektur sistem dan spesifikasi teknis bersama Ananda Hafiz Aulia, khususnya pada perancangan skema hak akses *user* dan *group* Linux.
4. **Penyusunan Laporan Subbab 3.6 (Keamanan & Docker):** Menulis pembahasan teknis, langkah pengujian, serta analisis mekanisme keamanan akses Linux dan isolasi kontainer Docker pada laporan akhir kelompok.
5. **Pengisian Matriks Kontribusi:** Mendokumentasikan pembagian tugas dan bukti kontribusi ke dalam matriks evaluasi tim.

---

## 4. Bukti Kontribusi

Seluruh hasil pengujian, log keluaran terminal, dan dokumentasi telah diarsipkan dan dapat diverifikasi melalui tautan serta berkas berikut pada repositori utama kelompok:

* **Commit Bukti Pengujian:** 
  * Pesan Commit: `"Bukti Waldan"`
  * Tautan Commit: [https://github.com/iiqbalitoo/proyekSO_5B_Kel5/commit/684fb58a85f7fbc46340b28409a37cd9ca1025eb](https://github.com/iiqbalitoo/proyekSO_5B_Kel5/commit/684fb58a85f7fbc46340b28409a37cd9ca1025eb)
* **Berkas Log & Artefak Pengujian:**
  * `evidence/waldan-akses.txt` — Log pengujian penolakan akses terhadap pengguna tidak berhak (`tamu`).
  * `evidence/waldan-docker.txt` — Log pengujian eksekusi kontainer Docker *bind mount* mode *read-only* (pembacaan berhasil dan penulisan ditolak).
  * `output/laporan.txt` — Berkas keluaran ringkasan laporan sistem yang dijadikan objek pengujian *mount* pada kontainer.
* **Dokumentasi Laporan Akhir Kelompok:**
  * **Bab 2 (Desain Sistem & Spesifikasi):** Bagian perancangan struktur direktori dan matriks hak akses *user/group*.
  * **Subbab 3.6 (Keamanan & Docker):** Bagian implementasi dan verifikasi pembatasan akses serta isolasi *bind mount* Docker.

---

## 5. Skenario Pengujian Individu

### A. Tujuan Pengujian
Membuktikan bahwa sistem menerapkan pembatasan hak akses secara ketat terhadap akun pengguna yang tidak berhak, serta memastikan kontainer Docker (`alpine:3.20`) mampu membaca berkas `output/laporan.txt` dari *host* melalui mekanisme *bind mount* bersifat *read-only*, namun ditolak secara sistem ketika mencoba menulis atau mengubah isi berkas tersebut.

### B. Kondisi Positif (Pembacaan Laporan via Kontainer Read-Only)
Menguji kemampuan kontainer ringan untuk mengakses dan menampilkan isi ringkasan laporan dari direktori `output/` pada *host* secara aman tanpa menyimpan kontainer secara permanen:

```bash
docker run --rm --mount type=bind,src="$(pwd)/output",dst=/data,readonly alpine:3.20 cat /data/laporan.txt
```
* **Hasil yang Diharapkan:** Kontainer berjalan sementara, membaca berkas `/data/laporan.txt`, menampilkan seluruh isi laporan ke standar keluaran (*stdout*) dengan status keluar (*exit code*) `0`, lalu otomatis terhapus setelah selesai.

### C. Kondisi Negatif (Penolakan Akses Akun Tamu & Penulisan pada Mount Read-Only)
1. **Pengujian Pembatasan Akun Tidak Berhak:**
   Menjalankan skrip pengajuan pekerjaan menggunakan akun `tamu` yang bukan merupakan pemilik maupun anggota grup pengelola sistem:
   ```bash
   sudo -u tamu bash scripts/ajukan.sh tests/ok.sh
   ```
   * **Hasil yang Diharapkan:** Sistem menolak eksekusi dan penulisan ke antrean dengan pesan galat `Permission denied` sehingga integritas direktori antrean terjaga.

2. **Pengujian Upaya Modifikasi Berkas dari Dalam Kontainer Read-Only:**
   Mencoba menyisipkan atau menimpa teks ke dalam berkas `/data/laporan.txt` di dalam kontainer yang terpasang dengan opsi `readonly`:
   ```bash
   docker run --rm --mount type=bind,src="$(pwd)/output",dst=/data,readonly alpine:3.20 sh -c 'echo "uji-tulis" >> /data/laporan.txt'
   ```
   * **Hasil yang Diharapkan:** Proses penulisan gagal dengan pesan galat `Read-only file system`, menghasilkan *exit status* bukan nol (`!= 0`), dan berkas asli `output/laporan.txt` pada *host* tetap utuh tanpa perubahan sedikit pun.

### D. Verifikasi
Seluruh keluaran terminal (*stdout* dan *stderr*), kode status keluar (*exit code*), serta pemeriksaan integritas berkas sebelum dan sesudah pengujian telah dicatat secara lengkap di dalam berkas `evidence/waldan-akses.txt` dan `evidence/waldan-docker.txt`, serta dilengkapi tangkapan layar pada laporan Subbab 3.6.

---

## 6. Analisis Konsep Sistem Operasi

1. **Proteksi Akses Berbasis User, Group, dan Permission di Linux:**
   Linux menerapkan model kontrol akses diskresioner (*Discretionary Access Control* / DAC) yang membagi kepemilikan objek menjadi tiga entitas: pemilik (*user/owner*), kelompok (*group*), dan pengguna lainnya (*others*), dengan tiga jenis izin dasar yaitu baca (`r`), tulis (`w`), dan eksekusi (`x`). Dengan membatasi izin tulis dan eksekusi direktori kerja hanya kepada *user* dan *group* proyek (misalnya mode `770` atau `750`), akun di luar grup seperti `tamu` otomatis terisolasi oleh kernel saat mencoba memanggil *system call* untuk membaca atau menulis ke direktori sistem antrean.

2. **Perbedaan Image dan Container:**
   * **Image** adalah templat statis bersifat *read-only* yang berisi sistem berkas minimal, pustaka, dependensi, dan konfigurasi aplikasi (dalam pengujian ini adalah `alpine:3.20`).
   * **Container** adalah instansiasi aktif (*runtime instance*) dari sebuah *image* yang berjalan sebagai proses terisolasi di atas kernel *host* dengan menambahkan lapisan tulis sementara (*writable layer*) di atas lapisan *image*.

3. **Fungsi Flag `--rm` pada Eksekusi Kontainer:**
   Secara bawaan, ketika sebuah kontainer berhenti berjalan (*exited*), sistem berkas dan metadatanya tetap tersimpan di disk *host*. Penggunaan opsi `--rm` menginstruksikan Docker daemon untuk secara otomatis menghapus kontainer beserta lapisan tulis sementaranya segera setelah proses utama di dalamnya selesai dieksekusi, sehingga mencegah penumpukan sampah kontainer (*dangling containers*) selama pengujian berulang.

4. **Pemetaan Direktori Host ke `/data` Melalui Bind Mount Read-Only:**
   Mekanisme *bind mount* memetakan jalur direktori spesifik pada sistem berkas *host* (`$(pwd)/output`) langsung ke titik kait (*mount point*) di dalam sistem berkas kontainer (`/data`). Dengan menambahkan parameter `readonly` (atau `ro`), kernel menerapkan pembatasan pada tingkat VFS (*Virtual File System*) untuk titik kait tersebut sehingga seluruh operasi *system call* yang bersifat memodifikasi data (seperti `write`, `unlink`, atau `chmod`) akan langsung ditolak oleh kernel dengan kode galat `EROFS` (*Read-only file system*).

5. **Batas Cakupan Keamanan Bind Mount Read-Only:**
   Meskipun *bind mount* mode *read-only* efektif mencegah perubahan data di direktori tujuan dari dalam kontainer, mekanisme ini **bukanlah batas isolasi keamanan mutlak**. Kontainer masih berbagi kernel yang sama dengan sistem *host*. Jika sebuah kontainer dijalankan dengan hak akses berlebihan (seperti `--privileged`), menggunakan akun `root` tanpa pemetaan *user namespace*, atau terdapat kerentanan eksploitasi kernel (*container escape*), proses di dalam kontainer berpotensi melewati pembatasan titik kait tersebut. Oleh karena itu, praktik keamanan berlapis tetap diperlukan.

---

## 7. Kendala dan Penyelesaian

Selama proses pengujian keamanan dan isolasi sistem pada lingkungan Ubuntu WSL2, terdapat beberapa kendala teknis yang berhasil diidentifikasi dan diselesaikan secara bertahap:

1. **Perintah Docker Tidak Dikenali pada Terminal WSL2:**
   * **Kendala:** Muncul pesan galat `Command 'docker' not found` saat tahap inisialisasi kontainer karena integrasi antara Docker Desktop pada Windows dengan distro Ubuntu di WSL2 belum diaktifkan.
   * **Penyelesaian:** Mengaktifkan opsi *WSL Integration* untuk distro Ubuntu melalui menu **Settings > Resources > WSL Integration** pada aplikasi Docker Desktop, kemudian melakukan *restart* pada layanan Docker engine.

2. **Penolakan Akses ke Docker Daemon API:**
   * **Kendala:** Setelah perintah `docker` dikenali, muncul pesan galat `permission denied while trying to connect to the docker API` akibat akun pengguna aktif belum terdaftar atau sesi terminal belum memperbarui keanggotaan grup `docker`.
   * **Penyelesaian:** Menambahkan akun pengguna Linux ke dalam grup `docker` menggunakan perintah `sudo usermod -aG docker $USER`, memulai ulang sesi terminal, serta memastikan pengujian kontainer dapat dijalankan dengan hak akses yang sesuai.

3. **Berkas Sumber Laporan Belum Tersedia Saat Mounting:**
   * **Kendala:** Saat melakukan pengujian *read-only bind mount* ke kontainer `alpine:3.20`, sistem menampilkan galat `No such file or directory` karena berkas `output/laporan.txt` belum dibangkitkan di direktori kerja.
   * **Penyelesaian:** Menjalankan skrip pembangkit laporan terlebih dahulu melalui perintah `bash scripts/laporan.sh` agar berkas `output/laporan.txt` tersedia sebelum direktori `output/` dilampirkan ke dalam kontainer.

4. **Perbedaan Riwayat Commit Saat Unggah Bukti ke Repositori Kelompok:**
   * **Kendala:** Ketika hendak mengunggah berkas bukti pengujian ke repositori GitHub kelompok yang dikerjakan secara paralel oleh anggota lain, terjadi penolakan *push* akibat adanya pembaruan baru di *remote repository*.
   * **Penyelesaian:** Menerapkan alur kerja sinkronisasi menggunakan perintah `git pull --rebase --autostash` sebelum melakukan `git commit` dan `git push`, sehingga seluruh berkas bukti berhasil terunggah secara rapi tanpa menimbulkan konflik penggabungan (*merge conflict*).

---

## 8. Refleksi Pembelajaran

### Hal yang Sudah Dikuasai
* Memahami penerapan nyata manajemen hak akses *user*, *group*, dan *permission* di lingkungan Linux untuk membatasi akses antar-pengguna pada sistem berbasis skrip.
* Mampu mengoperasikan kontainer Docker ringan (`alpine`) serta menerapkan konfigurasi *bind mount* dalam mode *read-only* untuk kebutuhan isolasi pembacaan data.
* Memahami alur kolaborasi Git dalam tim (penggunaan *pull rebase* dan *autostash*) serta pendokumentasian bukti pengujian teknis secara terstruktur dan dapat diaudit.

### Hal yang Masih Perlu Dipelajari
* **Pengamanan Tingkat Lanjut pada Kontainer:** Saya masih perlu memperdalam konfigurasi keamanan kontainer yang lebih ketat, seperti penerapan *rootless Docker*, pengaturan *user namespace remapping*, serta pembatasan *Linux capabilities* (`--cap-drop`) agar isolasi tidak hanya bergantung pada mode *read-only mount*.
* **Otomatisasi Pengujian Keamanan:** Pada proyek ini, pengujian pembatasan akses masih dijalankan secara manual langkah demi langkah. Ke depannya, saya ingin mempelajari cara menyusun skrip pengujian otomatis (*automated security test suite*) yang langsung memvalidasi *exit code* dan integritas berkas secara terprogram.


