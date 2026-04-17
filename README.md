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

<img width="1302" height="337" alt="image" src="https://github.com/user-attachments/assets/ab56012f-4d10-4d04-b8cd-13f20d11a8f7" />

3. **Exploitation:** Mengubah URL menjadi `/edit/7`. Karena proteksi hanya ada pada tampilan (*view*) tapi tidak pada fungsi edit, halaman terbuka dan Flag ditemukan di kode sumber.

<img width="1204" height="541" alt="Screenshot 2026-04-17 085621" src="https://github.com/user-attachments/assets/22c9a1ee-8170-4d7f-9176-ab40a19df5cd" />
4. **Pelajaran:** Jangan hanya mengandalkan satu titik keamanan; pastikan setiap fungsi (view, edit, delete) memiliki pengecekan izin yang sama.

---

### Flag ID: Flag #2 - Stored XSS (Cross-Site Scripting)
#### Tahapan (Step-by-Step):
1. **Identifikasi Input:** Menemukan fitur "Create a new page" yang menerima input teks.
2. **Uji Coba:** Memasukkan kode script `<script>alert('XSS')</script>` ke dalam kolom Title.
3. **Eksekusi:** Setelah halaman disimpan, kembali ke halaman utama. Script tereksekusi secara otomatis dan memunculkan pop-up berisi Flag.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a2623f5d-38a1-4a62-a507-c9a581a84a74" />

4. **Pelajaran:** Selalu lakukan sanitasi pada setiap input pengguna sebelum data tersebut disimpan ke database dan ditampilkan kembali.

---

### Flag ID: Flag #3 - XSS Bypass
#### Tahapan (Step-by-Step):
1. **Analisis Filter:** Website memberikan peringatan bahwa script tidak didukung.
2. **Bypass:** Menggunakan tag HTML alternatif yang memiliki atribut *event handler*, yaitu `<img src=x onerror=alert(1)>`.

<img width="1272" height="828" alt="image" src="https://github.com/user-attachments/assets/57e8e8f8-5cad-4aaf-b2d6-c60898997e8a" />

3. **Eksekusi:** Script berhasil dijalankan karena filter hanya mencari kata kunci `<script>`. Flag ditemukan di dalam Page Source (Ctrl+U).

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dffe75fe-4def-4e29-94b0-e67661406e85" />


---

### Flag ID: Flag #4 - SQL Injection (SQLi)
#### Tahapan (Step-by-Step):
1. **Triggering Error:** Menambahkan tanda petik satu (`'`) di akhir URL, misalnya `/edit/1'`.
2. **Analisis Respon:** Server mengalami error dan menampilkan pesan kesalahan database yang sangat detail.

<img width="1164" height="227" alt="image" src="https://github.com/user-attachments/assets/b557fd0e-710a-47c9-b768-fad34a2801ec" />


3. **Temuan:** Memeriksa Page Source pada halaman error tersebut dan menemukan Flag yang "terbocor" akibat gangguan pada logika database.
   
4. **Pelajaran:** Matikan pesan error detail di sisi produksi agar tidak memberikan informasi berharga kepada penyerang.



======================================================================================================================

Nama Level: Micro-CMS v2

