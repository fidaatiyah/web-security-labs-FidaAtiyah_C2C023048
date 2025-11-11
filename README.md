# web-security-labs-FidaAtiyah_C2C023048
Web Security Virtual Lab
Platform pembelajaran interaktif untuk memahami kerentanan keamanan aplikasi web dan cara mitigasinya.
Tentang Project
Web Security Virtual Lab adalah platform edukasi yang dikembangkan untuk membantu mahasiswa dan developer memahami kerentanan keamanan web berdasarkan OWASP Top 10. Platform ini menyediakan lingkungan terkontrol untuk mempelajari serangan dan teknik mitigasi.
Dikembangkan oleh: Fida Atiyah (C2C023048)
Dosen Pengampu: Dr. Dhendra Marutho S.Kom, M.Kom
Program Studi: Informatika - Universitas Muhammadiyah Semarang

Instalasi
Prerequisites

XAMPP (Apache + MySQL + PHP 7.4+)
Web Browser modern
Text Editor

Langkah Instalasi

Download atau clone project ke folder htdocs XAMPP
Import database security_lab.sql melalui phpMyAdmin
Edit konfigurasi database di config.php
Start Apache dan MySQL di XAMPP
Akses via browser: http://localhost/VirtualLab/index.php


Struktur Folder
VirtualLab/
├── index.php                    # Homepage utama
├── config.php                   # Konfigurasi database
├── safe/                        # Versi AMAN
│   ├── login_safe.php
│   ├── comment_safe.php
│   ├── upload_safe.php
│   └── profile_safe.php
├── vulnerable/                  # Versi RENTAN
│   ├── login_vulnerable.php
│   ├── comment_vulnerable.php
│   ├── upload_vulnerable.php
│   └── profile_vulnerable.php
└── uploads/                     # Folder upload

Perbedaan Versi Vulnerable dan Safe
1. SQL INJECTION
VERSI VULNERABLE
php$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = mysqli_query($conn, $query);

Input langsung digabung ke query
Tidak ada sanitasi
Payload admin' OR '1'='1 berhasil bypass login

VERSI SAFE
php$query = "SELECT * FROM users WHERE username = ? AND password = ?";
$stmt = $pdo->prepare($query);
$stmt->execute([$username, $password]);

Menggunakan prepared statements
Parameter binding otomatis escape input
Payload diperlakukan sebagai string literal, login gagal


2. CROSS-SITE SCRIPTING (XSS)
VERSI VULNERABLE
phpecho $row['comment']; // Langsung ditampilkan

Output tanpa encoding
Payload <script>alert('XSS')</script> dieksekusi
Script berjalan di browser setiap halaman dimuat

VERSI SAFE
phpecho htmlspecialchars($row['comment'], ENT_QUOTES, 'UTF-8');

Output di-encode sebelum ditampilkan
< menjadi &lt;, > menjadi &gt;
Script ditampilkan sebagai text, tidak dieksekusi


3. FILE UPLOAD VULNERABILITY
VERSI VULNERABLE
php$target = 'uploads/' . $_FILES['file']['name'];
move_uploaded_file($_FILES['file']['tmp_name'], $target);

Tidak ada validasi ekstensi
File PHP dapat diupload dan dieksekusi
Web shell shell.php berhasil memberikan akses RCE

VERSI SAFE
php$allowed = ['jpg', 'jpeg', 'png', 'gif'];
$ext = strtolower(pathinfo($_FILES['file']['name'], PATHINFO_EXTENSION));

if (!in_array($ext, $allowed)) {
    die("File type not allowed");
}

if ($_FILES['file']['size'] > 5242880) {
    die("File too large");
}

$newName = uniqid() . '_' . time() . '.' . $ext;
move_uploaded_file($_FILES['file']['tmp_name'], 'uploads/' . $newName);

Whitelist ekstensi yang diizinkan
Validasi ukuran file
Filename di-random untuk mencegah prediksi
File PHP ditolak


4. BROKEN ACCESS CONTROL (IDOR)
VERSI VULNERABLE
php$id = $_GET['id'];
$query = "SELECT * FROM users WHERE id = $id";
$result = mysqli_query($conn, $query);

Tidak ada pengecekan ownership
User dengan ID 101 bisa akses data user ID 102
URL profile.php?id=102 menampilkan data user lain

VERSI SAFE
php$requestedId = $_GET['id'];
$sessionUserId = $_SESSION['user_id'];

if ($requestedId != $sessionUserId) {
    http_response_code(403);
    die("Access Denied");
}

$query = "SELECT * FROM users WHERE id = ? AND id = ?";
$stmt->execute([$requestedId, $sessionUserId]);

Server-side authorization check
Validasi ownership sebelum query
Akses ke ID lain ditolak dengan error 403


Ringkasan Mitigasi
VulnerabilityTeknik Mitigasi UtamaSQL InjectionPrepared Statements + Parameter BindingXSSOutput Encoding dengan htmlspecialchars()File UploadWhitelist Extension + Size Limit + Filename RandomizationBroken Access ControlServer-Side Authorization + Ownership Check

Cara Penggunaan

Akses homepage dan pilih modul yang ingin dipelajari
Klik Vulnerable Version untuk melihat implementasi yang rentan
Coba berbagai payload untuk memahami cara kerja serangan
Klik Safe Version untuk melihat implementasi yang aman
Coba payload yang sama dan bandingkan hasilnya


Disclaimer
Platform ini dibuat untuk tujuan edukasi. Jangan gunakan teknik yang dipelajari untuk menyerang sistem tanpa izin. Penggunaan ilegal dapat melanggar hukum.
