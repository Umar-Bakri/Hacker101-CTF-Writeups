# Hacker101-CTF-Writeups
"Catatan perjalanan belajar cybersecurity dari nol."

# 🚩 Hacker101 CTF Writeups

## Nama Level: Micro-CMS v1
**Target URL:** [https://ctf.hacker101.com/launch/...]

---

### Flag ID: Flag #1 - IDOR (Insecure Direct Object Reference)
#### Tahapan (Step-by-Step):
1. **Analisis URL:** Saat membuka halaman, URL menunjukkan pola `/page/1`.
2. **Eksperimen:** Mencoba mengakses halaman yang tidak ada di menu, yaitu `/page/7`, namun mendapatkan respon "Forbidden".
3. **Exploitation:** Mengubah URL menjadi `/edit/7`. Karena proteksi hanya ada pada tampilan (*view*) tapi tidak pada fungsi edit, halaman terbuka dan Flag ditemukan di kode sumber.
4. **Pelajaran:** Jangan hanya mengandalkan satu titik keamanan; pastikan setiap fungsi (view, edit, delete) memiliki pengecekan izin yang sama.

---

### Flag ID: Flag #2 - Stored XSS (Cross-Site Scripting)
#### Tahapan (Step-by-Step):
1. **Identifikasi Input:** Menemukan fitur "Create a new page" yang menerima input teks.
2. **Uji Coba:** Memasukkan kode script `<script>alert('XSS')</script>` ke dalam kolom Title.
3. **Eksekusi:** Setelah halaman disimpan, kembali ke halaman utama. Script tereksekusi secara otomatis dan memunculkan pop-up berisi Flag.
4. **Pelajaran:** Selalu lakukan sanitasi pada setiap input pengguna sebelum data tersebut disimpan ke database dan ditampilkan kembali.

---

### Flag ID: Flag #3 - XSS Bypass
#### Tahapan (Step-by-Step):
1. **Analisis Filter:** Website memberikan peringatan bahwa script tidak didukung.
2. **Bypass:** Menggunakan tag HTML alternatif yang memiliki atribut *event handler*, yaitu `<img src=x onerror=alert(1)>`.
3. **Eksekusi:** Script berhasil dijalankan karena filter hanya mencari kata kunci `<script>`. Flag ditemukan di dalam Page Source (Ctrl+U).

---

### Flag ID: Flag #4 - SQL Injection (SQLi)
#### Tahapan (Step-by-Step):
1. **Triggering Error:** Menambahkan tanda petik satu (`'`) di akhir URL, misalnya `/edit/1'`.
2. **Analisis Respon:** Server mengalami error dan menampilkan pesan kesalahan database yang sangat detail.
3. **Temuan:** Memeriksa Page Source pada halaman error tersebut dan menemukan Flag yang "terbocor" akibat gangguan pada logika database.
   
5. **Pelajaran:** Matikan pesan error detail di sisi produksi agar tidak memberikan informasi berharga kepada penyerang.
