# Portal HMPS SI: Setup Online Gratis

Frontend di-host melalui GitHub Pages. Firebase Authentication mengelola login dan Cloud Firestore menyimpan data bersama lintas perangkat. Paket gratis memiliki kuota dan tunduk pada kebijakan penyedia; tidak ada jaminan layanan gratis tanpa batas waktu.

## 1. Buat Firebase project

1. Buat project di Firebase Console, lalu tambahkan aplikasi Web.
2. Salin konfigurasi aplikasi Web ke blok `firebaseConfig` di file HTML. Ganti `ISI_API_KEY`, `ISI_PROJECT_ID`, dan `ISI_APP_ID`; `authDomain` gunakan `<project-id>.firebaseapp.com`.
3. Di Authentication, aktifkan penyedia **Email/Password**.
4. Buat dua akun di Authentication:
   - Email `sekretaris@<project-id>.firebaseapp.com`
   - Email `bendahara@<project-id>.firebaseapp.com`
   - Gunakan kata sandi baru yang kuat untuk masing-masing akun.
5. Salin UID tiap akun. Di Firestore, buat koleksi `users` dan dokumen dengan ID yang sama persis dengan UID. Isi field string `role` dengan `sekretaris` atau `bendahara` sesuai akunnya.
6. Buat database Firestore dan terbitkan aturan dari file `firestore.rules`.
7. Di Authentication settings, tambahkan domain GitHub Pages, misalnya `<username>.github.io`, ke daftar authorized domains.

Aplikasi memasangkan username `sekretaris`/`bendahara` dengan email di atas. Kata sandi tidak disimpan di HTML. Dokumen `users/{uid}` hanya boleh dibaca pemiliknya dan tidak bisa diubah dari aplikasi.

## 2. Publikasikan dengan GitHub Pages

1. Buat repository GitHub dan unggah file HTML ini sebagai `index.html`, beserta `firestore.rules` untuk referensi.
2. Pada repository, buka **Settings → Pages**, pilih deploy dari branch `main` dan folder `/root`, lalu simpan.
3. Tunggu GitHub menampilkan alamat situs Pages, lalu buka alamat itu dan uji login kedua role.

Repository Pages gratis biasanya harus public. Kode frontend dan konfigurasi Firebase akan terlihat publik; itu normal untuk Firebase Web. Keamanan data berasal dari Firestore rules, jadi jangan pernah memasukkan password, service account key, atau kredensial admin ke HTML/repository. Data aplikasi disimpan di Firestore, bukan di repository.

## 3. Pindahkan data lokal lama

1. Buka versi HTML di perangkat/browser lama yang menyimpan data.
2. Pada layar login, tekan **Unduh backup JSON** sebelum berpindah ke situs Pages.
3. Pada situs Pages, tekan **Pulihkan backup JSON** dan pilih file backup tersebut.
4. Login sebagai Sekretaris dan tunggu data tersinkron. Logout, lalu login sebagai Bendahara di browser yang sama untuk menyinkronkan data keuangannya.

Login pertama akan mengisi kumpulan Firestore yang belum ada dari backup lokal. Setelah tersinkron, perangkat lain mengambil data dari Firestore saat login. File backup berisi data organisasi; simpan dengan aman dan hapus setelah migrasi selesai.

## Data dan akses

Firestore menyimpan dataset Sekretaris dan Bendahara di jalur terpisah. Aturan di `firestore.rules` membatasi setiap role ke datanya sendiri. Data Firestore tiap dataset disimpan sebagai satu dokumen; jika data berkembang mendekati batas ukuran dokumen Firestore, struktur penyimpanan perlu dipecah menjadi dokumen per rapat/transaksi.