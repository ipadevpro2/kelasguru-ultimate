# Google Spreadsheet Structure Guide

Panduan ini menjelaskan struktur Google Spreadsheet yang akan digunakan sebagai database untuk aplikasi Administrasi Guru. Setiap sheet dalam spreadsheet mewakili satu entitas dalam sistem.

## Pembuatan Google Spreadsheet

1. Buka [Google Sheets](https://sheets.google.com)
2. Buat spreadsheet baru
3. Buat sheet terpisah untuk setiap entitas yang disebutkan di bawah
4. Untuk setiap sheet, tambahkan header kolom seperti yang ditentukan
5. **Penting**: Setiap sheet HARUS memiliki kolom "id" sebagai pengidentifikasi unik

## Struktur Sheet dan Kolom

### 1. Kelas

Sheet ini menyimpan data tentang kelas-kelas yang ada.

| Nama Sheet | Kelas |
|------------|-------|

**Kolom:**
- id (number) - ID unik untuk setiap kelas
- nama (string) - Nama kelas (contoh: "XI IPA 1")
- wali_kelas (string) - Nama wali kelas
- tahun_ajaran (string) - Tahun ajaran (contoh: "2023/2024")
- jumlah_siswa (number) - Jumlah siswa dalam kelas

### 2. Siswa

Sheet ini menyimpan data tentang siswa.

| Nama Sheet | Siswa |
|------------|-------|

**Kolom:**
- id (number) - ID unik untuk setiap siswa
- nama (string) - Nama lengkap siswa
- kelas_id (number) - ID kelas tempat siswa berada (referensi ke Kelas.id)
- jenis_kelamin (string) - Jenis kelamin siswa ("L" atau "P")
- alamat (string) - Alamat siswa
- no_telp (string) - Nomor telepon/kontak siswa
- email (string) - Alamat email siswa
- tanggal_lahir (date) - Tanggal lahir siswa
- foto (string) - URL foto siswa (opsional)

### 3. Tugas

Sheet ini menyimpan data tentang tugas yang diberikan.

| Nama Sheet | Tugas |
|------------|-------|

**Kolom:**
- id (number) - ID unik untuk setiap tugas
- judul (string) - Judul tugas
- deskripsi (string) - Deskripsi detail tugas
- kelas_id (number) - ID kelas yang mendapat tugas (referensi ke Kelas.id)
- tanggal_dibuat (date) - Tanggal tugas dibuat
- deadline (date) - Tenggat waktu pengumpulan tugas
- materi_id (number) - ID materi yang terkait (opsional)

### 4. Nilai

Sheet ini menyimpan data nilai siswa untuk tugas tertentu.

| Nama Sheet | Nilai |
|------------|-------|

**Kolom:**
- id (number) - ID unik untuk setiap entri nilai
- siswa_id (number) - ID siswa (referensi ke Siswa.id)
- tugas_id (number) - ID tugas (referensi ke Tugas.id)
- nilai (number) - Nilai numerik (0-100)
- catatan (string) - Catatan tambahan dari guru
- tanggal_penilaian (date) - Tanggal penilaian dilakukan

### 5. Presensi

Sheet ini menyimpan data presensi/kehadiran.

| Nama Sheet | Presensi |
|------------|----------|

**Kolom:**
- id (number) - ID unik untuk setiap entri presensi
- tanggal (date) - Tanggal presensi
- kelas_id (number) - ID kelas (referensi ke Kelas.id)
- data_presensi (string) - Data presensi dalam format JSON, contoh: 
  ```json
  {"1": "H", "2": "I", "3": "S", "4": "A"}
  ```
  Dimana kunci adalah siswa_id dan nilai adalah status kehadiran:
  - H: Hadir
  - I: Izin
  - S: Sakit
  - A: Alpha/Tanpa Keterangan

### 6. Event

Sheet ini menyimpan data acara atau kegiatan sekolah.

| Nama Sheet | Event |
|------------|-------|

**Kolom:**
- id (number) - ID unik untuk setiap event
- nama (string) - Nama event
- deskripsi (string) - Deskripsi detail event
- tanggal_mulai (date) - Tanggal mulai event
- tanggal_selesai (date) - Tanggal selesai event
- lokasi (string) - Lokasi event
- penyelenggara (string) - Nama penyelenggara (opsional)

### 7. JurnalPembelajaran

Sheet ini menyimpan data jurnal pembelajaran.

| Nama Sheet | JurnalPembelajaran |
|------------|------------------|

**Kolom:**
- id (number) - ID unik untuk setiap jurnal
- tanggal (date) - Tanggal pembelajaran
- kelas_id (number) - ID kelas (referensi ke Kelas.id)
- materi (string) - Materi yang diajarkan
- kegiatan (string) - Rincian kegiatan pembelajaran
- evaluasi (string) - Evaluasi pembelajaran
- catatan (string) - Catatan tambahan

### 8. BankSoal

Sheet ini menyimpan data bank soal untuk ujian atau latihan.

| Nama Sheet | BankSoal |
|------------|----------|

**Kolom:**
- id (number) - ID unik untuk setiap soal
- pertanyaan (string) - Teks pertanyaan
- jawaban (string) - Jawaban atau kunci jawaban
- tingkat_kesulitan (string) - Tingkat kesulitan soal (Mudah/Sedang/Sulit)
- kategori (string) - Kategori soal (contoh: Matematika, Fisika, dll)
- materi_id (number) - ID materi terkait (opsional)

## Contoh Setup Awal

Untuk memulai, Anda bisa membuat beberapa data contoh di setiap sheet:

1. **Kelas**: Tambahkan beberapa kelas seperti "XI IPA 1", "XI IPA 2", dll.
2. **Siswa**: Tambahkan beberapa data siswa dengan referensi ke kelas yang sesuai
3. **Tugas**: Tambahkan beberapa contoh tugas untuk kelas-kelas tersebut

## Mendapatkan ID Spreadsheet

Setelah membuat spreadsheet dengan struktur di atas, Anda perlu mendapatkan ID spreadsheet untuk digunakan dalam kode Google Apps Script:

1. Buka Google Spreadsheet Anda
2. Perhatikan URL di browser:
   ```
   https://docs.google.com/spreadsheets/d/YOUR_SPREADSHEET_ID_HERE/edit
   ```
3. Salin bagian `YOUR_SPREADSHEET_ID_HERE` dan ganti di file `code.gs`:
   ```javascript
   const SPREADSHEET_ID = 'YOUR_SPREADSHEET_ID_HERE';
   ```

## Izin Akses

Pastikan Google Apps Script memiliki akses ke spreadsheet:
1. Jika Anda menggunakan spreadsheet dan script dengan akun yang sama, ini biasanya tidak menjadi masalah
2. Jika berbeda akun, pastikan untuk memberi izin akses ke spreadsheet untuk akun yang menjalankan script 