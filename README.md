# Document Control - PT Energi Batubara Lestari

Website pengendalian dokumen ISO 9001, 14001, dan 45001. Berjalan di GitHub Pages tanpa server sendiri.

## Isi folder

| File | Fungsi | Perlu diubah? |
| --- | --- | --- |
| `index.html` | Aplikasi utama | Tidak |
| `config.js` | Pengaturan akun dan Firebase | Ya, satu-satunya file yang diubah |
| `firebase-rules.json` | Aturan keamanan database, ditempel ke Firebase | Tidak (kecuali username diganti) |
| `tools/buat-hash.html` | Pembuat hash kata sandi untuk mode lokal | Tidak |
| `data.json` | Data dokumen mode lokal. **Tidak ada di paket ini** supaya data Anda yang sudah ada tidak tertimpa | Biarkan |

Aplikasi punya dua mode. Mode yang aktif tertulis kecil di bawah tombol Masuk.

| | Mode lokal | Mode realtime |
| --- | --- | --- |
| Aktif bila | `dbUrl` dan `apiKey` di `config.js` kosong | Keduanya terisi |
| Data disimpan di | `data.json` di GitHub | Firebase Realtime Database |
| File dokumen | Ditempel sebagai link | Diunggah langsung dari website |
| Perubahan terlihat pengguna lain | Setelah `data.json` diunggah ulang | Saat itu juga |
| Kata sandi diperiksa oleh | Hash di `config.js` | Firebase Authentication |

---

## A. Pasang ke GitHub

1. Buka repositori `docoebl/Document-Control-PT-Energi-Batubara-Lestari` di GitHub.
2. Klik **Add file > Upload files**.
3. Seret `index.html`, `config.js`, `firebase-rules.json`, `README.md`, dan folder `tools` ke halaman unggah. Jangan unggah `data.json` baru, biarkan yang lama.
4. Klik **Commit changes**.
5. Tunggu 1 sampai 2 menit, buka website, lalu tekan **Ctrl+Shift+R**.

Setelah langkah ini, login mode lokal sudah berfungsi dengan kata sandi sementara yang Anda terima. Segera ganti (bagian C).

---

## B. Mengaktifkan realtime (Firebase)

Perlu akun Google. Paket gratis Firebase (Spark) cukup untuk pemakaian ini. Nama menu di Firebase Console bisa sedikit berbeda dari yang tertulis di sini.

### 1. Buat proyek

1. Buka https://console.firebase.google.com dan klik **Create a project**.
2. Beri nama, misalnya `doc-control-ebl`. Google Analytics boleh dimatikan.

### 2. Buat database

1. Menu **Build > Realtime Database > Create Database**.
2. Lokasi: **Singapore (asia-southeast1)**.
3. Pilih **Start in locked mode**, lalu **Enable**.
4. Salin alamat database di bagian atas tab Data. Bentuknya seperti
   `https://doc-control-ebl-default-rtdb.asia-southeast1.firebasedatabase.app`. Ini nilai **dbUrl**.

### 3. Pasang aturan keamanan

1. Masih di Realtime Database, buka tab **Rules**.
2. Hapus semua isinya, tempel seluruh isi file `firebase-rules.json`.
3. Klik **Publish**.

Dengan aturan ini, daftar dokumen (nomor, judul, status) bisa dibaca tanpa login, tetapi **isi file hanya bisa dibuka setelah login** dengan salah satu dari tiga akun. Yang bisa menulis hanya akun `docoebl` dan `dept`.

Setiap kali `firebase-rules.json` berubah (misalnya setelah memperbarui website), ulangi langkah ini.

### 4. Aktifkan login dan buat akun

1. Menu **Build > Authentication > Get started**.
2. Tab **Sign-in method**, pilih **Email/Password**, aktifkan, lalu **Save**.
3. Tab **Users**, klik **Add user**, buat tiga akun berikut dengan kata sandi pilihan Anda (minimal 6 karakter):

   | Email di Firebase | Username saat login di website | Hak |
   | --- | --- | --- |
   | `docoebl@ebl.app` | `docoebl` | Document Controller, akses penuh |
   | `dept@ebl.app` | `dept` | Departemen, tambah dan ubah dokumen folder 11 dan 12, isi bukti perbaikan |
   | `viewer@ebl.app` | `viewer` | Lihat saja |

   Email ini tidak perlu benar-benar ada. Di website, pengguna cukup mengetik username.
4. Tab **Settings > User actions**, hilangkan centang **Enable create (sign-up)**, lalu **Save**. Ini mencegah orang luar mendaftar sendiri.

Buat ketiga akun sebelum membagikan link website.

### 5. Ambil apiKey

1. Klik ikon roda gigi di kiri atas, pilih **Project settings**.
2. Di tab **General**, salin **Web API Key**.
3. Kalau belum muncul: gulir ke **Your apps**, klik ikon **</>**, daftarkan aplikasi web dengan nama bebas. Nilai `apiKey` tampil di kode yang ditunjukkan.

### 6. Isi config.js

1. Di GitHub, buka `config.js`, klik ikon pensil.
2. Isi kedua nilai di antara tanda kutip:

   ```js
   cloud: {
       dbUrl: 'https://doc-control-ebl-default-rtdb.asia-southeast1.firebasedatabase.app',
       apiKey: 'AIzaSy................................',
       emailDomain: 'ebl.app'
   },
   ```
3. Klik **Commit changes**.

### 7. Coba

1. Tunggu 1 sampai 2 menit, buka website, tekan **Ctrl+Shift+R**.
2. Di bawah tombol Masuk harus tertulis **Mode realtime (Firebase)**.
3. Login sebagai `docoebl` dengan kata sandi yang dibuat di Firebase.
4. Kalau database masih kosong, muncul tawaran mengimpor isi `data.json` lama. Pilih **OK** agar data lama pindah ke Firebase.
5. Di kanan atas muncul penanda hijau **Realtime**. Buka website di perangkat lain, ubah satu dokumen, dan perubahan muncul di perangkat pertama dalam beberapa detik.

---

## Dashboard

Halaman pertama setelah login menampilkan tiga hal:

- **Jumlah dokumen** terdaftar, dengan pembagian Approved, dalam review, dan status lain.
- **Kelengkapan dokumen tiap departemen.** Setiap departemen perlu punya minimal satu dokumen untuk tujuh jenis dokumen (KPI, Job Description, IBPR IADL, SOP, Form, IK, STD). Persentasenya adalah jumlah jenis dokumen yang sudah terisi dibagi tujuh. Dokumen berstatus Obsolete tidak dihitung.
- **Temuan audit internal dan eksternal**, dengan persentase capaian, yaitu temuan yang hasil review-nya sudah "Sesuai" dibagi seluruh temuan.

Daftar seluruh dokumen lintas folder ada di menu **Semua Dokumen**.

---

## Folder 8: Struktur Organisasi dan Job Description

Folder 8 berisi satu folder untuk setiap departemen, dikelompokkan Head Office (HO) dan Site, ditambah folder "Struktur organisasi perusahaan" untuk bagan organisasi tingkat perusahaan. Di dalam tiap departemen tampil daftar posisinya; posisi yang sudah punya dokumen ditandai centang, dan persentase di kartu departemen menunjukkan berapa posisi yang sudah punya job description.

Saat menambah dokumen ke folder 8, pilih departemen dan (untuk job description) posisinya. Daftar departemen dan posisi ada di `SO_UNITS` di `index.html`.

Nama reviewer pada review dokumen dan review temuan audit selalu "Document Control" dan tidak bisa diubah.

---

## Unggah dokumen langsung

Pada mode realtime, kolom "Link Dokumen" diganti kolom **File Dokumen**. File dipilih dari komputer dan disimpan di Firebase, tidak perlu SharePoint.

- Jenis file: PDF, Word, Excel, PowerPoint, PNG, JPG. Maksimal **15 MB** per file.
- Tombol **Buka** hanya menampilkan dokumen di layar, untuk semua akun, tanpa tombol unduh atau cetak. Semua jenis file yang bisa diunggah bisa dilihat: PDF, gambar, **Word (.docx), Excel (.xlsx), dan PowerPoint (.pptx)**.
- Word, Excel, dan PowerPoint digambar ulang di browser, jadi tampilannya disederhanakan: huruf, jarak, dan posisi bisa sedikit berbeda dari aplikasi Office, dan grafik, SmartArt, serta gambar berformat EMF/WMF tidak ditampilkan. Untuk dokumen yang tampilannya harus persis (misalnya formulir bertanda tangan), unggah sebagai PDF.
- Format Office lama (.doc, .xls, .ppt) tidak bisa ditampilkan di browser, jadi tidak bisa diunggah lagi. Simpan ulang di Office sebagai .docx, .xlsx, atau .pptx (File > Save As) sebelum diunggah. File lama yang sudah terlanjur diunggah tetap bisa diunduh Document Controller.
- Tombol **Unduh** (ikon panah ke bawah): Document Controller mendapat file asli. Akun **Departemen selalu mendapat salinan berformat PDF dengan watermark hitam "TIDAK TERKENDALI"** di setiap halaman, dan nama filenya ikut diberi tanda. Gambar (PNG, JPG) otomatis dijadikan PDF satu halaman.
- Word, Excel, PowerPoint, dan PDF yang terkunci kata sandi tidak bisa diubah menjadi PDF ber-watermark di website ini, jadi hanya bisa diunduh Document Controller. Akun lain tetap bisa melihatnya lewat tombol Buka. Unggah dokumen terkendali sebagai **PDF** (di Word: File > Save As > PDF) agar bisa dilihat dan diunduh semua akun.
- **Bukti perbaikan temuan audit bisa lebih dari satu file**, paling banyak 20 file per temuan. Pilih beberapa file sekaligus, atau tekan "Pilih file" berulang kali; daftar file yang akan diunggah tampil sebelum disimpan. Bukti yang sudah diunggah tampil di tabel temuan, di jendela unggah bukti, dan di jendela review. Menambah bukti tidak menghapus bukti lama. Hanya Document Controller yang bisa menghapus file bukti (lewat tombol edit temuan).
- Bukti perbaikan temuan audit bukan dokumen terkendali, jadi diunduh tanpa watermark (hanya Document Controller dan Departemen).
- Akun **Viewer hanya bisa melihat dokumen di layar** dan tidak punya tombol Unduh. Untuk mengizinkan Viewer mengunduh salinan ber-watermark, ubah `VIEWER_CAN_DOWNLOAD = false` menjadi `true` di `index.html`. Teks watermark ada di `WATERMARK_TEXT`, kepekatannya di `WATERMARK_OPACITY`.
- Saat menambah dokumen, pilih dulu **dokumen apa yang diunggah**: Dokumen ISO umum atau Dokumen departemen. Untuk dokumen umum, pilih jenisnya (folder 1 sampai 10). Untuk dokumen departemen, pilih departemen dan jenis dokumennya (KPI, Job Description, IBPR IADL, SOP, Form, IK, STD). Baris "Disimpan ke" menunjukkan folder tujuannya.
- Di folder 11 dan 12, setiap departemen berisi folder per jenis dokumen. Dokumen yang diunggah otomatis masuk ke folder jenisnya.
- Saat merevisi dokumen, unggah file revisi lewat tombol edit. File revisi lama tetap bisa dibuka dari riwayat revisi.
- Bukti perbaikan temuan audit diunggah dengan cara yang sama.
- Dokumen lama yang masih berupa link tetap bisa dibuka seperti biasa.
- Menghapus dokumen ikut menghapus semua file revisinya.

**Kapasitas.** Paket gratis Firebase menyediakan 1 GB. Karena cara penyimpanannya, itu setara kira-kira **700 MB file asli**. Pantau pemakaian di Firebase Console > Realtime Database > tab **Usage**. Kalau mendekati penuh, hapus dokumen usang atau pindah ke paket berbayar.

---

## Pindah ke proyek Firebase lain

Gunakan `tools/pindah-data.html` (buka `alamat-website/tools/pindah-data.html`). Halaman ini login ke database lama dan database baru dengan akun Document Controller masing-masing, lalu menyalin isi semua file, daftar dokumen, dan temuan audit. Database lama tidak diubah. Kalau proses terputus, jalankan lagi; file yang sudah tersalin dilewati. Setelah selesai, ganti `dbUrl` dan `apiKey` di `config.js` ke proyek baru.

---

## C. Mengganti kata sandi

**Mode realtime:** Firebase Console > Authentication > Users > titik tiga di baris akun > **Reset password**. Tidak ada file yang perlu diunggah.

**Mode lokal:**

1. Buka `tools/buat-hash.html` (klik dua kali file-nya di komputer, atau buka `alamat-website/tools/buat-hash.html`).
2. Pilih akun, ketik kata sandi baru, klik **Buat hash**.
3. Tempel hash ke baris akun tersebut di `config.js`, lalu **Commit changes**.

Jangan menulis kata sandi asli di file mana pun. Repositori ini bisa dibaca publik.

---

## D. Menambah atau mengganti nama akun

1. Tambahkan baris baru di bagian `users` pada `config.js`. `role` harus `admin`, `dept`, atau `viewer`. Username huruf kecil tanpa spasi.
2. Mode realtime: buat juga akunnya di Firebase Authentication sebagai `username@ebl.app`.
3. Mode realtime: aturan di `firebase-rules.json` hanya mengizinkan `docoebl@ebl.app` dan `dept@ebl.app` menulis. Kalau username admin atau departemen diganti atau ditambah, ubah juga email di aturan tersebut lalu **Publish** ulang.

---

## E. Kalau ada masalah

| Gejala | Penyebab dan solusi |
| --- | --- |
| "Username atau kata sandi salah" | Username harus salah satu yang ada di `config.js`. Mode lokal: buat ulang hash lewat `tools/buat-hash.html`. Mode realtime: reset kata sandi di Firebase. |
| "apiKey di config.js tidak valid" | Salin ulang Web API Key, pastikan tidak ada spasi atau karakter terpotong. |
| "Login Email/Password belum diaktifkan" | Ulangi langkah B.4. |
| "Server menolak perubahan (kode 401 atau 403)" | Aturan belum dipasang (B.3), atau akun tidak punya hak menulis. |
| "Unggahan ditolak server" atau "Akses file ditolak server" | Aturan di Firebase masih versi lama. Tempel ulang isi `firebase-rules.json` terbaru di tab Rules lalu Publish. |
| Kolom yang muncul "Link Dokumen", bukan "File Dokumen" | Website masih mode lokal. Unggah file hanya ada pada mode realtime. |
| "Ukuran file melebihi batas 15 MB" | Perkecil file (misalnya kompres PDF) atau pecah menjadi beberapa dokumen. |
| "Gagal membaca data (kode 401)" | Aturan belum dipasang atau `dbUrl` salah. |
| Tertulis "Mode lokal" padahal config sudah diisi | `config.js` belum ter-commit, atau browser masih memakai versi lama. Tunggu 2 menit lalu Ctrl+Shift+R. |
| Penanda bertuliskan "Tersinkron hh:mm:ss", bukan "Realtime" | Jaringan memblokir koneksi langsung. Aplikasi otomatis memeriksa data setiap 10 detik, jadi tetap berfungsi. |
| "Login membutuhkan koneksi aman (https)" | Website dibuka lewat `http://`. Gunakan alamat `https://`. |
| Perubahan file belum terlihat | GitHub Pages perlu 1 sampai 2 menit. Coba jendela penyamaran (incognito). |

---

## Catatan keamanan

- **Mode lokal hanya penghalang tampilan.** Semua pemeriksaan terjadi di browser, sehingga orang yang paham teknis bisa melewatinya. Pakai mode ini hanya sementara.
- **Mode realtime benar-benar membatasi siapa yang bisa mengubah data**, karena penulisan diperiksa oleh server Firebase.
- Pada mode realtime, daftar dokumen (nomor, judul, status) bisa dibaca siapa pun yang tahu alamat database. Isi file yang diunggah hanya bisa dibuka setelah login.
- `apiKey` Firebase memang dirancang untuk terlihat publik. Yang melindungi data adalah aturan di `firebase-rules.json`.
- Batas paket gratis Firebase: 100 koneksi bersamaan, 1 GB data tersimpan, 10 GB unduhan per bulan.
- Sesekali cadangkan data: Firebase Console > Realtime Database > titik tiga > **Export JSON**.
