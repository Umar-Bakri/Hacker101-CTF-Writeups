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



# Flag ID: Flag #2 - Blind SQL Injection (Data Exfiltration)
# Tahapan (Step-by-Step):
1. **Analisis Fitur:** Pada tantangan tingkat Moderate ini, teknik Authentication Bypass sederhana tidak cukup untuk mendapatkan semua flag. Sistem memerlukan kredensial asli (username & password) yang tersimpan di database untuk menampilkan flag tertentu.
2. **Observasi Teknik:** Saya mengidentifikasi adanya celah Blind SQL Injection pada kolom login. Karena server tidak menampilkan pesan error atau data mentah, saya menggunakan logika Boolean-based untuk "bertanya" kepada database melalui perbedaan ukuran respon (Length) di Burp Suite.
3. **Exploitation (Finding Length):** Menggunakan Burp Suite Intruder, saya mengirimkan payload untuk menentukan panjang karakter username dan password.

Payload Username: username=' OR LENGTH(username)=§1§ # (Ditemukan: 7 Karakter)

Payload Password: username=' OR LENGTH(password)=§1§ # (Ditemukan: 6 Karakter)

4. **Exploitation (Blind SQLi - Data Exfiltration):** Setelah mengetahui panjang karakter, saya melakukan ekstraksi data karakter demi karakter menggunakan operator LIKE dan teknik chaining.

# Mantra: username=' OR username LIKE '§a§%' #&password=test
jika huruf pertama sudah di temukan maka codedenya jadi begini misal huruf pertama c
Mantra: username=' OR username LIKE **'c§a§%'** #&password=test dan teruskan hingga sesuai panjang username
begitu juga dengan password **Mantra: username=' OR password LIKE '§a§%' #&password=test**
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/859ee78b-2e33-4d42-b6d1-4d0729cd135d" />
<img width="1312" height="666" alt="image" src="https://github.com/user-attachments/assets/10f3c451-6908-4308-a025-8c3804bcca08" />

Saya menebak huruf pertama, menguncinya, lalu melanjutkan ke huruf berikutnya hingga seluruh string terungkap.

5. **Temuan:** Dengan melakukan login secara sah menggunakan username carylon dan password venice, sistem memberikan otorisasi penuh dan Flag ditemukan di dalam dashboard admin.


# Flag ID: Flag #2 HTTP Verb Tampering atau Method Manipulation

## 🛠️ Langkah-langkah sesuai Video

### 1. Buka Halaman Edit
- Pergi ke halaman utama **Micro-CMS v2**, lalu klik salah satu halaman (misal: *Micro-CMS Changelog*).
- Klik link **"Edit this page"**.
- Kamu bakal dilempar ke halaman **Login** karena sistem minta izin admin.

### 2. Tangkap Request di Burp Suite
- Di Burp Suite, pastikan **Intercept is ON**.
- Klik lagi link **"Edit this page"** di browser.
- Di Burp, kamu bakal dapet request yang bentuknya:
  <img width="1920" height="1080" alt="Screenshot 2026-04-26 132135" src="https://github.com/user-attachments/assets/1de6d43f-053a-4495-aa27-53db3549bae4" />
# GET /page/edit/1 HTTP/1.1

### 3. Gunakan Repeater (Trik Intinya)
- Klik kanan pada request itu, pilih **Send to Repeater**.
- Pindah ke tab **Repeater**.
- Di baris paling atas, **ganti kata `GET` menjadi `POST`**:
<img width="1920" height="1080" alt="Screenshot 2026-04-26 132154" src="https://github.com/user-attachments/assets/b798d090-2867-4f11-9780-415260e6ba54" />

# POST /page/edit/1 HTTP/1.1
### 4. Eksekusi
- Klik tombol **Send**.
- Lihat di bagian **Response**.
- **BOOM!** Karena server cuma nge-blok akses `GET` tapi lupa nge-blok akses `POST`, **Flag-nya langsung muncul** di sana tanpa kamu perlu login sama sekali!

---

## 🕵️ Kenapa Cara Ini Berhasil?

Ini karena **kelalaian programmer-nya**. Mereka cuma pasang *"satpam"* di pintu masuk `GET`, tapi pintu `POST` dibiarkan **terbuka lebar**. Jadi kita bisa masuk lewat **pintu belakang**. 🚪
<img width="1920" height="1080" alt="Screenshot 2026-04-26 132217" src="https://github.com/user-attachments/assets/bf286d7c-89e9-4355-a539-5c0f39fadae1" />


  

