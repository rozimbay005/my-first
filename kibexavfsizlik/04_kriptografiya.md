# 📘 4-BOB: KRIPTOGRAFIYA ASOSLARI

---

## 4.1 Kriptografiya nima?

Kriptografiya — ma'lumotni faqat ruxsat berilgan shaxs o'qiy olishi uchun maxfiy kodga aylantirish fani.

Oddiy misol:
```
Ochiq matn:    "Salom dunyo"
Shifrlangan:   "Vdorp gxqbr"   ← Boshqa odam tushunmaydi
Kalit bilan:   "Salom dunyo"   ← Kalit bilan qaytariladi
```

**Kiberxavfsizlikda nima uchun kerak?**
- 🔐 Parollar qanday saqlanishini tushunish
- 🌐 HTTPS qanday ishlashini tushunish
- 🔑 Token va sessiyalarni buzish
- 💣 Zaif kriptografiyani topish
- 🔓 Hash cracking (parol buzish)

---

## 4.2 Asosiy tushunchalar

| Atama | Ma'nosi |
|-------|---------|
| **Plaintext** | Ochiq (shifrsiz) matn |
| **Ciphertext** | Shifrlangan matn |
| **Encryption** | Shifrlash (ochiq → yopiq) |
| **Decryption** | Shifrni ochish (yopiq → ochiq) |
| **Key (Kalit)** | Shifrlash/ochish uchun maxfiy kod |
| **Algorithm** | Shifrlash usuli (AES, RSA...) |
| **Hash** | Bir tomonlama o'zgartirish |
| **Salt** | Hashga qo'shiladigan tasodifiy qiymat |

---

## 4.3 Simmetrik shifrlash

Simmetrik shifrlashda **bitta kalit** ham shifrlash, ham ochish uchun ishlatiladi.

```
Yuboruvchi: Matn + Kalit → Shifrlangan matn
Qabul qiluvchi: Shifrlangan matn + Kalit → Matn
```

**Asosiy algoritmlar:**

### AES (Advanced Encryption Standard)
Hozirgi kunda eng kuchli va keng tarqalgan algoritm.

| AES turi | Kalit uzunligi | Xavfsizlik |
|----------|---------------|-----------|
| AES-128 | 128 bit | ✅ Yaxshi |
| AES-192 | 192 bit | ✅ Juda yaxshi |
| AES-256 | 256 bit | ✅ Eng yuqori |

**Kali da AES shifrlash:**
```bash
# Faylni AES-256 bilan shifrlash
openssl enc -aes-256-cbc -in ochiq.txt -out shifrlangan.bin -k "maxfiykalit"

# Shifrni ochish
openssl enc -aes-256-cbc -d -in shifrlangan.bin -out ochiq.txt -k "maxfiykalit"
```

### DES va 3DES (Eskirgan!)
- DES — 56 bitli kalit, **buzilgan!** Ishlatmang
- 3DES — 168 bitli, hali ishlatiladi lekin sekin

### Blowfish
- Tez va xavfsiz, ba'zi tizimlarda hali ishlatiladi

---

## 4.4 Asimmetrik shifrlash (Ochiq kalit kriptografiyasi)

Asimmetrik shifrlashda **ikki ta kalit** bor:
- **Ochiq kalit (Public Key)** — hamma biladigan kalit
- **Maxfiy kalit (Private Key)** — faqat siz bilasigan kalit

```
Kim bo'lsa ham sizga xabar yuborishi mumkin:
  Matn + SIZNING OCHIQ KALITINGIZ → Shifrlangan

Faqat siz ocha olasiz:
  Shifrlangan + SIZNING MAXFIY KALITINGIZ → Matn
```

### RSA
Eng mashhur asimmetrik algoritm.

```bash
# RSA kalit juftligi yaratish
openssl genrsa -out maxfiy.pem 2048        # Maxfiy kalit (2048 bit)
openssl rsa -in maxfiy.pem -pubout -out ochiq.pem  # Ochiq kalit

# Ochiq kalit bilan shifrlash
openssl rsautl -encrypt -inkey ochiq.pem -pubin -in matn.txt -out shifrlangan.bin

# Maxfiy kalit bilan ochish
openssl rsautl -decrypt -inkey maxfiy.pem -in shifrlangan.bin -out ochiq_matn.txt

# Kalit ma'lumotlarini ko'rish
openssl rsa -in maxfiy.pem -text -noout
```

### ECC (Elliptic Curve Cryptography)
RSA dan kichikroq kalit bilan xuddi shunday xavfsizlik:
- RSA 2048 bit ≈ ECC 224 bit
- Telefon va qurilmalarda ko'p ishlatiladi

### Raqamli imzo (Digital Signature)
```
Yuboruvchi: Matn + MAXFIY KALIT → Imzo
Qabul qiluvchi: Matn + Imzo + OCHIQ KALIT → "Haqiqiy!"
```

Bu orqali:
- ✅ Kim yuborganini aniqlaymiz
- ✅ Matn o'zgartirilmaganini tekshiramiz

---

## 4.5 Hash funksiyalar

Hash — ma'lumotni **qaytarib bo'lmaydigan** qisqa qiymatga aylantiradi.

```
"Salom"     → 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
"Salom!"    → 2de8e05ed9e636e24c11e40f16b7060b5f9aa0a5d3e1dfc3eb2e2d5b6a6d9df2

# Bitta harf farqi → butunlay boshqa hash!
```

**Hash xususiyatlari:**
- ✅ Bir tomonlama (hashdan asliga qaytib bo'lmaydi)
- ✅ Deterministik (bir xil matn → har doim bir xil hash)
- ✅ Avalanche effect (kichik o'zgarish → katta farq)
- ✅ Tez hisoblash

### Mashhur hash algoritmlari:

| Algoritm | Uzunlik | Holati |
|----------|---------|--------|
| MD5 | 128 bit (32 hex) | ❌ Buzilgan! |
| SHA-1 | 160 bit (40 hex) | ⚠️ Zaif |
| SHA-256 | 256 bit (64 hex) | ✅ Xavfsiz |
| SHA-512 | 512 bit (128 hex) | ✅ Juda xavfsiz |
| bcrypt | Variable | ✅ Parol uchun eng yaxshi |
| Argon2 | Variable | ✅ Hozirgi eng yaxshisi |

**Kali da hash hisoblash:**
```bash
# MD5
echo -n "Salom" | md5sum
md5sum fayl.txt

# SHA-256
echo -n "Salom" | sha256sum
sha256sum fayl.txt

# SHA-512
echo -n "Salom" | sha512sum

# Barcha bir vaqtda
echo -n "password" | md5sum
echo -n "password" | sha1sum
echo -n "password" | sha256sum
```

---

## 4.6 Hash Cracking (Parol buzish)

Bu yerda biz **o'z tizimimizni** sinash uchun hash cracking o'rganamiz.

### Hash turini aniqlash

```bash
# hash-identifier bilan
hash-identifier
# Hash ni yozing, u turini aytadi

# Yoki hashcat bilan
hashcat --identify hash.txt
```

**Hash ko'rinishlari:**
```
MD5:    5f4dcc3b5aa765d61d8327deb882cf99  (32 belgi)
SHA-1:  5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8  (40 belgi)
SHA-256: 5e884898da28047151d0e56f8dc6292773603d0d...  (64 belgi)
bcrypt: $2a$12$R9h/cIPz0gi.URNNX3kh2OPST9/PgBkqquzi.Ss7KIUgO2t0jWMUW
```

### Hashcat — Hash Cracker

```bash
# Asosiy ishlatish
hashcat -m 0 hash.txt wordlist.txt        # MD5
hashcat -m 100 hash.txt wordlist.txt      # SHA-1
hashcat -m 1400 hash.txt wordlist.txt     # SHA-256
hashcat -m 1800 hash.txt wordlist.txt     # SHA-512
hashcat -m 3200 hash.txt wordlist.txt     # bcrypt

# -m raqamlari (hash turi)
# 0     = MD5
# 100   = SHA-1
# 1400  = SHA-256
# 1700  = SHA-512
# 1800  = sha512crypt (Linux parollar)
# 3200  = bcrypt
# 5500  = NetNTLMv1 (Windows)
# 5600  = NetNTLMv2 (Windows)
# 13100 = Kerberoast (Active Directory)

# Wordlist hujumi
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

# Brute force hujumi
hashcat -m 0 -a 3 hash.txt ?a?a?a?a?a?a  # 6 ta har qanday belgi

# Mask hujumi (format ma'lum bo'lsa)
hashcat -m 0 -a 3 hash.txt Pass?d?d?d?d  # Pass + 4 raqam

# Kombinatsiya hujumi
hashcat -m 0 -a 1 hash.txt wordlist1.txt wordlist2.txt

# Natijani ko'rish
hashcat -m 0 hash.txt --show
```

**Mask belgilari:**
```
?l = kichik harf    (a-z)
?u = katta harf     (A-Z)
?d = raqam          (0-9)
?s = maxsus belgi   (!@#$...)
?a = hammasi        (?l?u?d?s)
```

### John the Ripper

```bash
# Linux /etc/shadow faylini buzish
sudo cat /etc/shadow > shadow.txt
john shadow.txt

# Wordlist bilan
john --wordlist=/usr/share/wordlists/rockyou.txt shadow.txt

# ZIP parolini buzish
zip2john himoyalangan.zip > zip_hash.txt
john zip_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

# PDF parolini buzish
pdf2john himoyalangan.pdf > pdf_hash.txt
john pdf_hash.txt

# Natijani ko'rish
john --show shadow.txt
```

### Wordlistlar

```bash
# Kali da mavjud wordlistlar
ls /usr/share/wordlists/
ls /usr/share/wordlists/rockyou.txt    # Eng mashhuri (14M+ parol)

# RockYou ni ochish (siqilgan bo'lsa)
gunzip /usr/share/wordlists/rockyou.txt.gz

# O'z wordlist yaratish
crunch 6 8 abcdefghijklmnopqrstuvwxyz0123456789 -o wordlist.txt
# 6 = min uzunlik, 8 = max uzunlik

# CeWL — saytdan wordlist yaratish
cewl https://example.com -d 2 -m 5 -w wordlist.txt
# -d = chuqurlik, -m = min so'z uzunligi
```

---

## 4.7 SSL/TLS — HTTPS qanday ishlaydi

HTTPS = HTTP + TLS shifrlash

**TLS Handshake jarayoni:**
```
Brauzer                    Server
   |                          |
   |--- ClientHello ---------->|  "TLS 1.3 ishlatamiz, mana cipher list"
   |<-- ServerHello -----------|  "Yaxshi, mana sertifikat"
   |<-- Certificate -----------|  (RSA ochiq kalit bor)
   |                          |
   |  [Sertifikatni tekshiradi]|
   |  [Session kalit yaratadi] |
   |                          |
   |--- ClientKeyExchange ---->|  (Session kalitni RSA bilan shifrlaydi)
   |--- ChangeCipherSpec ----->|  "Endi AES ishlatamiz"
   |--- Finished ------------->|
   |<-- ChangeCipherSpec ------|
   |<-- Finished --------------|
   |                          |
   |=== AES shifrlangan aloqa =|
```

**SSL/TLS tekshirish:**
```bash
# Sayt sertifikatini ko'rish
openssl s_client -connect google.com:443

# Sertifikat ma'lumotlari
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -text

# Sertifikat muddati
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates

# TLS versiyasini tekshirish
nmap --script ssl-enum-ciphers -p 443 google.com

# SSLyze bilan tekshirish
sslyze google.com
```

---

## 4.8 Encoding va Decoding

Encoding — shifrlash emas! Bu ma'lumotni boshqa formatga o'tkazish.

### Base64
```bash
# Encode
echo -n "Salom dunyo" | base64
# Natija: U2Fsb20gZHVueW8=

# Decode
echo "U2Fsb20gZHVueW8=" | base64 -d
# Natija: Salom dunyo

# Fayl encode
base64 rasm.png > rasm_b64.txt
base64 -d rasm_b64.txt > qayta_rasm.png
```

### URL Encoding
```
Bo'sh joy  = %20
!          = %21
@          = %40
#          = %23
/          = %2F
```

```bash
# Python bilan URL decode
python3 -c "import urllib.parse; print(urllib.parse.unquote('Salom%20dunyo'))"

# URL encode
python3 -c "import urllib.parse; print(urllib.parse.quote('Salom dunyo'))"
```

### Hex Encoding
```bash
# Matnni hex ga
echo -n "Salom" | xxd
echo -n "Salom" | hexdump -C

# Hex dan matnga
echo "53616c6f6d" | xxd -r -p
```

### ROT13 (Caesar cipher)
```bash
echo "Salom dunyo" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
# Natija: Fnybz qhail

# Qayta
echo "Fnybz qhail" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
# Natija: Salom dunyo
```

---

## 4.9 Steganografiya

Steganografiya — ma'lumotni boshqa ma'lumot ichida yashirish.

```bash
# Rasm ichiga fayl yashirish
steghide embed -cf rasm.jpg -sf maxfiy.txt -p "kalit123"

# Yashirilgan faylni chiqarish
steghide extract -sf rasm.jpg -p "kalit123"

# Rasm ichida yashirin ma'lumot bormi?
steghide info rasm.jpg

# Strings buyrugi bilan binarda matn qidirish
strings rasm.jpg | grep -i "flag"
strings fayl.bin | grep -E "[A-Za-z0-9]{10,}"

# Exiftool — metadata ko'rish
exiftool rasm.jpg
```

---

## 4.10 Amaliy: Parol xavfsizligi

```bash
# 1. Parolni hashlab ko'ring
echo -n "password123" | md5sum
echo -n "password123" | sha256sum

# 2. Bu hashni cracklang
echo "482c811da5d5b4bc6d497ffa98491e38" > test_hash.txt
hashcat -m 0 test_hash.txt /usr/share/wordlists/rockyou.txt

# 3. Kuchli parol yarating
openssl rand -base64 16

# 4. bcrypt hash (Python bilan)
python3 -c "import bcrypt; print(bcrypt.hashpw(b'mypassword', bcrypt.gensalt()).decode())"

# 5. Linux parol hashini ko'ring
sudo cat /etc/shadow | grep kali
# Format: $6$salt$hash
# $1$ = MD5
# $5$ = SHA-256
# $6$ = SHA-512
# $2a$ = bcrypt
```

---

## 4.11 GPG — GNU Privacy Guard

```bash
# GPG kalit juftligi yaratish
gpg --gen-key

# Mavjud kalitlarni ko'rish
gpg --list-keys

# Faylni shifrlash (ochiq kalit bilan)
gpg --encrypt --recipient "email@example.com" fayl.txt

# Faylni ochish (maxfiy kalit bilan)
gpg --decrypt fayl.txt.gpg

# Faylni imzolash
gpg --sign fayl.txt

# Imzoni tekshirish
gpg --verify fayl.txt.gpg

# Kalit eksport
gpg --export --armor email@example.com > ochiq_kalit.asc
```

---

## 4.12 Bob yakunida bilishingiz kerak

✅ Simmetrik vs asimmetrik shifrlash farqi  
✅ AES, RSA algoritmlarini tushunish  
✅ Hash nima va qanday ishlaydi  
✅ MD5, SHA-256, bcrypt farqlari  
✅ Hashcat va John bilan hash cracking  
✅ Base64, URL, Hex encoding/decoding  
✅ SSL/TLS qanday ishlashini tushunish  
✅ Steganografiya asoslari  

---

## 📝 Amaliy topshiriq #4

```bash
# 1. Quyidagi hashlarni crack qiling:
# MD5:   5f4dcc3b5aa765d61d8327deb882cf99
# MD5:   e10adc3949ba59abbe56e057f20f883e
# SHA-1: 0ade7c2cf97f75d009975f4d720d1fa6c19f4897
echo "5f4dcc3b5aa765d61d8327deb882cf99" > hash1.txt
hashcat -m 0 hash1.txt /usr/share/wordlists/rockyou.txt --force

# 2. O'z parolingizni SHA-256 bilan hashlang
echo -n "SIZNING_PAROLINGIZ" | sha256sum

# 3. Base64 decode qiling:
echo "SGFja2VyIG1hbmdh" | base64 -d

# 4. Steghide bilan rasm ichiga xabar yashiring
# (Kali da: sudo apt install steghide)
echo "Bu yashirin xabar" > yashirin.txt
steghide embed -cf /usr/share/pixmaps/kali-menu.png -sf yashirin.txt -p "kalit"

# 5. Linux shadow faylini o'rganing
sudo cat /etc/shadow | head -5
# $6$ = SHA-512 ishlatilayapti
```

---

*Keyingi bob → 🌐 Web xavfsizligi (OWASP Top 10)*
