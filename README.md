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







### Nama Level:Micro-CMS v2
### Flag ID: Flag #1 - SQL Injection (Authentication Bypass)
#### Tahapan (Step-by-Step):

1. **Triggering Error:** Memasukkan tanda petik satu (`'`) pada kolom username di form login.
2. **Analisis Respon:** Server merespon dengan "Internal Server Error (500)", yang mengonfirmasi bahwa input tanda petik merusak struktur kueri SQL di backend (*vulnerable*).
3. **Exploitation:** Menggunakan payload `' UNION SELECT 'x' -- ` di kolom username untuk memanipulasi hasil database agar mengembalikan password palsu berupa karakter 'x'.

<img width="1215" height="451" alt="image" src="https://github.com/user-attachments/assets/3137326c-1f2d-4076-9f70-03a681fec862" />
4. **Eksekusi:** Mengisi kolom password dengan `x` agar cocok dengan hasil manipulasi database. Login berhasil dan sistem memberikan akses admin.
5. **Temuan:** Flag ditemukan di dalam menu "Private Page" yang baru muncul setelah login berhasil.


### Flag ID: Flag #2 - Insecure Direct Object Reference (IDOR)
## Tahapan (Step-by-Step):
1. **Analisis Fitur:** Setelah berhasil masuk sebagai admin menggunakan teknik SQL Injection 
saya mengakses fitur pengeditan halaman untuk melihat bagaimana sistem memproses permintaan data.

2. **Observasi URL:** Memperhatikan struktur URL pada address bar saat berada di halaman edit 
yaitu /edit/1. Hal ini mengindikasikan bahwa aplikasi memanggil konten berdasarkan ID numerik yang dikirimkan melalui URL.

3. **Exploitation (Parameter Tampering):** Melakukan pengujian dengan mengganti angka ID pada URL secara manual. 
Saya mencoba mengakses ID yang tidak muncul di menu utama, yaitu dengan mengubah URL menjadi /page/3.
<img width="1287" height="418" alt="image" src="https://github.com/user-attachments/assets/e988537c-446b-4e9b-96ed-26f6f91f7b3b" />

5. **Eksekusi:** Karena kurangnya pengecekan otorisasi di sisi server, sistem memberikan akses ke halaman rahasia yang seharusnya tidak dapat diakses secara langsung.
6. **Temuan:** Flag ditemukan di dalam judul atau isi konten pada halaman rahasia tersebut.
7. **Pelajaran:** Keamanan tidak boleh hanya mengandalkan penyembunyian tautan (security through obscurity). Setiap fungsi yang memanggil objek berdasarkan ID harus melalui pengecekan izin (otorisasi) untuk memastikan pengguna memang berhak mengakses data tersebut.

