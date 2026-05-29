# 📘 2-BOB: TARMOQ ASOSLARI

---

## 2.1 Tarmoq nima?

Tarmoq — ikki yoki undan ortiq qurilmaning ma'lumot almashish uchun ulangan tizimi.

Misol:
- Uyingizdagi telefon, noutbuk, printer — barchasi Wi-Fi orqali ulangan → bu **LAN (Local Area Network)**
- Butun internet — dunyo bo'ylab tarmoqlarning to'plami → bu **WAN (Wide Area Network)**

Kiberxavfsizlik uchun tarmoqni tushunish **ENG MUHIM** asos hisoblanadi. Chunki:
- Hujumlarning 90% tarmoq orqali amalga oshiriladi
- Trafikni tahlil qila bilsangiz — hujumni aniqlay olasiz

---

## 2.2 OSI Modeli — 7 qatlam

OSI (Open Systems Interconnection) modeli tarmoq aloqasini 7 qatlamga bo'ladi. Bu modelni **yoddan bilish** shart!

```
7 - APPLICATION    ← Foydalanuvchi ko'radigan qatlam (HTTP, FTP, DNS)
6 - PRESENTATION   ← Ma'lumot formati (SSL/TLS, shifrlash, siqish)
5 - SESSION        ← Ulanish boshqaruvi (sessiya ochish/yopish)
4 - TRANSPORT      ← Ma'lumot yetkazish (TCP, UDP, portlar)
3 - NETWORK        ← Yo'naltirish (IP manzillar, Router)
2 - DATA LINK      ← Fizik aloqa (MAC manzil, Switch)
1 - PHYSICAL       ← Kabel, signal, bit uzatish
```

### Yodlash uchun ibora (inglizcha):
**"All People Seem To Need Data Processing"**
- **A**ll → Application
- **P**eople → Presentation
- **S**eem → Session
- **T**o → Transport
- **N**eed → Network
- **D**ata → Data Link
- **P**rocessing → Physical

---

### Har bir qatlamni batafsil tushunish:

#### 🔴 7-qatlam: Application (Ilova)
Foydalanuvchi bevosita ishlaydigan qatlam.

**Protokollar:**
| Protokol | Port | Vazifasi |
|----------|------|---------|
| HTTP | 80 | Veb saytlar |
| HTTPS | 443 | Xavfsiz veb saytlar |
| FTP | 21 | Fayl yuborish |
| SSH | 22 | Xavfsiz masofaviy kirish |
| SMTP | 25 | Email yuborish |
| POP3 | 110 | Email olish |
| IMAP | 143 | Email sinxronlash |
| DNS | 53 | Domen → IP aylantirish |
| DHCP | 67/68 | Avtomatik IP berish |
| Telnet | 23 | Masofaviy kirish (xavfsiz emas!) |

---

#### 🟠 6-qatlam: Presentation (Taqdimot)
Ma'lumotni formatlash, shifrlash va siqish.

- **SSL/TLS** — HTTPS da ma'lumotni shifrlaydi
- **Base64** — ikkilik ma'lumotni matn ko'rinishiga o'tkazadi
- **JPEG, PNG, MP4** — media formatlar

---

#### 🟡 5-qatlam: Session (Sessiya)
Ulanishni boshqaradi — ochadi, saqlab turadi, yopadi.

Misol: Siz bankingizga kirsangiz — sessiya ochiladi. Chiqsangiz — yopiladi.

---

#### 🟢 4-qatlam: Transport (Transport)
Ma'lumotni bo'laklarga bo'lib yuboradi va yig'adi.

**TCP vs UDP — farqini bilish JUDA muhim:**

| Xususiyat | TCP | UDP |
|-----------|-----|-----|
| Ishonchliligi | ✅ Yuqori | ❌ Past |
| Tezligi | 🐢 Sekinroq | ⚡ Tez |
| Tasdiqlash | ✅ Har paket tasdiqlanadi | ❌ Tasdiqlanmaydi |
| Ishlatilishi | HTTP, SSH, FTP | Video, o'yinlar, DNS |
| Ulanish | Ulanishli (3-way handshake) | Ulanishsiz |

**TCP 3-Way Handshake (Ulanish jarayoni):**
```
Mijoz          Server
  |--- SYN ------->|   "Salom, ulanmoqchi edim"
  |<-- SYN-ACK ----|   "Yaxshi, tayyorman"
  |--- ACK ------->|   "Ulanyapman"
  |    (Ulanish o'rnatildi)    |
```

**Portlar:**
- 0–1023: Well-known ports (tizim portlari)
- 1024–49151: Registered ports
- 49152–65535: Dynamic ports

---

#### 🔵 3-qatlam: Network (Tarmoq)
IP manzillar va yo'naltirish (routing).

---

#### 🟣 2-qatlam: Data Link
MAC manzillar va switchlar.

---

#### ⚫ 1-qatlam: Physical
Kabel, Wi-Fi signali, bitlar.

---

## 2.3 IP Manzillar

### IPv4
IPv4 — 32 bitli manzil, 4 oktetdan iborat.

```
Format: XXX.XXX.XXX.XXX
Misol:  192.168.1.100
```

Har bir oktet: 0 dan 255 gacha (8 bit = 2^8 = 256 ta qiymat)

**IP manzil turlari:**

| Tur | Diapazon | Ishlatilishi |
|-----|----------|-------------|
| **Private (Xususiy)** | 10.0.0.0/8 | Korporativ tarmoqlar |
| **Private** | 172.16.0.0/12 | Korporativ tarmoqlar |
| **Private** | 192.168.0.0/16 | Uy tarmoqlari |
| **Loopback** | 127.0.0.1 | O'z kompyuteri |
| **Public** | Qolganlar | Internet |

### Subnet Mask (Quyi tarmoq niqobi)
Subnet mask qaysi IP lar bir tarmoqda ekanligini ko'rsatadi.

```
IP:      192.168.1.100
Mask:    255.255.255.0   (/24)
Network: 192.168.1.0
Hosts:   192.168.1.1 — 192.168.1.254 (254 ta qurilma)
```

**CIDR notation:**
```
/24 = 255.255.255.0  → 254 host
/16 = 255.255.0.0    → 65534 host
/8  = 255.0.0.0      → 16 million host
/32 = bitta IP manzil
```

### IPv6
IPv6 — 128 bitli manzil, IPv4 manzillar tugab borayotganligi sababli yaratilgan.

```
Format: xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx
Misol:  2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

---

## 2.4 MAC Manzil

MAC (Media Access Control) — har bir tarmoq kartasining noyob fizik manzili.

```
Format: XX:XX:XX:XX:XX:XX
Misol:  00:1A:2B:3C:4D:5E
```

- Birinchi 3 oktet → ishlab chiqaruvchi (OUI)
- Oxirgi 3 oktet → qurilma raqami

**Kali da MAC manzilini ko'rish:**
```bash
ip link show
# yoki
ifconfig
```

**MAC manzilini o'zgartirish (spoofing):**
```bash
sudo ip link set eth0 down
sudo ip link set eth0 address XX:XX:XX:XX:XX:XX
sudo ip link set eth0 up
```

---

## 2.5 DNS — Domain Name System

DNS — domen nomlarini IP manzillarga aylantiradi.

```
google.com → 142.250.185.46
```

**DNS so'rov jarayoni:**
```
1. Siz: "google.com ga kir"
2. Brauzer: DNS cache tekshiradi
3. Topilmasa → Router ga so'raydi
4. Router → ISP DNS serveriga so'raydi
5. ISP → Root DNS → .com DNS → google.com DNS
6. Javob: 142.250.185.46
7. Brauzer: shu IP ga ulanadi
```

**DNS yozuvlari (Records):**

| Tur | Vazifasi | Misol |
|-----|---------|-------|
| **A** | Domen → IPv4 | google.com → 142.250.185.46 |
| **AAAA** | Domen → IPv6 | google.com → 2607:f8b0::... |
| **MX** | Email server | mail.google.com |
| **CNAME** | Alias (taxallus) | www → google.com |
| **TXT** | Matn ma'lumot | SPF, DKIM |
| **NS** | Name server | ns1.google.com |

**Kali da DNS so'rovlar:**
```bash
# A record
nslookup google.com

# Barcha recordlar
dig google.com ANY

# Reverse lookup (IP → domen)
dig -x 8.8.8.8

# NS record
dig google.com NS

# MX record
dig google.com MX
```

---

## 2.6 DHCP

DHCP (Dynamic Host Configuration Protocol) — tarmoqqa ulangan qurilmalarga avtomatik IP beradi.

**Jarayon (DORA):**
```
D — Discover: "Kimda IP bor?"    (Broadcast)
O — Offer:    "Menda bor, mana"  (Server javob)
R — Request:  "O'sha IP ni olaman" (Client)
A — Acknowledge: "OK, berildi"   (Server)
```

---

## 2.7 ARP — Address Resolution Protocol

ARP — IP manzilni MAC manzilga aylantiradi.

```bash
# ARP jadvalini ko'rish
arp -a

# yoki
ip neigh
```

**ARP Spoofing** — keyingi boblarda o'rganamiz (MITM hujumi uchun ishlatiladi)

---

## 2.8 Wireshark bilan tarmoq tahlili

Wireshark — tarmoq trafikini "ko'rish" imkonini beradi.

### O'rnatish (Kali da allaqachon bor):
```bash
wireshark
```

### Asosiy filtrlar:

```
# Faqat HTTP trafikni ko'rish
http

# Faqat bitta IP
ip.addr == 192.168.1.1

# Faqat TCP
tcp

# Faqat DNS
dns

# Faqat ping (ICMP)
icmp

# Bitta portni ko'rish
tcp.port == 80

# Ikki IP orasidagi trafik
ip.addr == 192.168.1.1 && ip.addr == 192.168.1.2

# HTTP GET so'rovlari
http.request.method == "GET"

# Parollarni qidirish (oddiy HTTP)
http contains "password"
```

### Amaliyot:
1. Wireshark ni oching
2. Tarmoq interfeysini tanlang (eth0 yoki wlan0)
3. Yashil "Start" tugmasini bosing
4. Brauzerda http://example.com ga kiring
5. Wireshark da HTTP paketlarni koring

---

## 2.9 Nmap — Tarmoq skaneri

Nmap — tarmoqdagi qurilmalar va portlarni aniqlash uchun.

```bash
# Bitta IP skanerlash
nmap 192.168.1.1

# Butun tarmoqni skanerlash
nmap 192.168.1.0/24

# Port versiyalarini aniqlash
nmap -sV 192.168.1.1

# OS aniqlash
nmap -O 192.168.1.1

# Eng to'liq skan
nmap -sC -sV -O 192.168.1.1

# Tez skan (eng mashhur 100 port)
nmap -F 192.168.1.1

# Barcha 65535 portni skanerlash
nmap -p- 192.168.1.1

# UDP skan
nmap -sU 192.168.1.1

# Stealth (yashirin) skan
nmap -sS 192.168.1.1

# Skript bilan skan
nmap --script=vuln 192.168.1.1
```

**Nmap natijasini o'qish:**
```
PORT     STATE    SERVICE    VERSION
22/tcp   open     ssh        OpenSSH 8.2
80/tcp   open     http       Apache 2.4.41
443/tcp  open     https      Apache 2.4.41
3306/tcp filtered mysql
8080/tcp closed   http-proxy
```

- `open` — port ochiq, xizmat ishlayapti
- `closed` — port yopiq
- `filtered` — firewall bloklayapti

---

## 2.10 Netcat — "Tarmoqning Shveytsariya pichog'i"

```bash
# Server yaratish (tinglash)
nc -lvp 4444

# Ulanish
nc 192.168.1.1 4444

# Fayl yuborish
nc -lvp 4444 > olingan_fayl.txt    # Qabul qiluvchi
nc 192.168.1.1 4444 < yuborilajak.txt  # Yuboruvchi

# Banner grabbing (xizmat ma'lumoti olish)
nc 192.168.1.1 80
GET / HTTP/1.1
Host: 192.168.1.1
```

---

## 2.11 Ping va Traceroute

```bash
# Ping — qurilma tirikmi?
ping 192.168.1.1
ping google.com -c 4     # 4 ta paket yuborish

# Traceroute — paket qaysi yo'ldan boradi?
traceroute google.com

# Windows da:
# tracert google.com
```

**Ping natijasini o'qish:**
```
PING google.com (142.250.185.46): 56 data bytes
64 bytes from 142.250.185.46: icmp_seq=0 ttl=117 time=15.3 ms
64 bytes from 142.250.185.46: icmp_seq=1 ttl=117 time=14.8 ms

--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss
round-trip min/avg/max = 14.8/15.0/15.3 ms
```

- `ttl` — Time To Live (paket necha routerdan o'tishi mumkin)
- `time` — javob vaqti (ms da, past bo'lsa yaxshi)
- `0% packet loss` — yo'qotish yo'q (yaxshi)

---

## 2.12 Amaliy mashqlar

### Mashq 1: Tarmoqni o'rganing
```bash
# O'z IP manzilingizni toping
ip addr show

# Tarmoq shlyuzini toping
ip route show

# DNS serveringizni toping
cat /etc/resolv.conf

# Tarmoqdagi qurilmalarni toping
nmap -sn 192.168.1.0/24
```

### Mashq 2: DNS tadqiqot
```bash
# Mashhur saytlar haqida ma'lumot
dig facebook.com ANY
dig youtube.com MX
nslookup twitter.com
whois google.com
```

### Mashq 3: Wireshark amaliyoti
1. Wireshark ni yoqing
2. `ping 8.8.8.8` bajaring
3. Wireshark da `icmp` filtrini qo'ying
4. ICMP paketlarni ko'ring
5. Bitta paketni bosib, ichidagi ma'lumotlarni o'qing

---

## 2.13 Bob yakunida bilishingiz kerak

✅ OSI modelining 7 qatlamini nomlari va vazifalari  
✅ TCP va UDP farqi  
✅ TCP 3-Way Handshake jarayoni  
✅ IP manzil tuzilishi va subnet mask  
✅ DNS qanday ishlashi  
✅ Nmap bilan port skanerlash  
✅ Wireshark bilan trafikni ko'rish  
✅ Netcat bilan tarmoqda ishlash  

---

## 📝 Amaliy topshiriq #2

1. Kali Linux terminalini oching
2. `nmap -sV 192.168.1.1` bajaring (router IP ingiz)
3. Wireshark oching va 2 daqiqa trafikni yozing
4. `dig google.com ANY` bajaring va natijani o'qing
5. TryHackMe da "Network Fundamentals" modulini tugating

---

*Keyingi bob → 🐧 Linux buyruqlari va tizim boshqaruvi*
