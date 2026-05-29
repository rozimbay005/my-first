# 📘 1-BOB: KIRISH VA MUHIT O'RNATISH

---

## 1.1 Kiberxavfsizlik nima?

Kiberxavfsizlik — bu kompyuter tizimlari, tarmoqlar, dasturlar va ma'lumotlarni ruxsatsiz kirish, hujum, shikastlash yoki o'g'irlashdan himoya qilish fani va amaliyotidir.

Oddiy qilib aytganda:
> Kimdir sizning kompyuteringizga, serveringizga yoki ma'lumotlaringizga zarar yetkazishga harakat qilsa — kiberxavfsizlik mutaxassisi uni to'xtatadi yoki oldini oladi.

---

## 1.2 Nima uchun kiberxavfsizlik o'rganish kerak?

- 💰 **Yuqori maosh**: O'rta darajali mutaxassis oyiga $3000–$8000 ishлайди
- 🌍 **Talab ko'p**: Dunyoda 3.5 million+ kiberxavfsizlik bo'sh ish o'rni mavjud
- 🧠 **Qiziqarli**: Har kuni yangi muammo, yangi yechim
- 🔐 **Muhim**: Har bir kompaniya, bank, davlat bu mutaxassislarga muhtoj

---

## 1.3 Kiberxavfsizlik turlari

### 🔴 Offensive Security (Hujum tomoni)
Tizimlarni "buzish" orqali zaifliklarni topish:
- **Penetration Tester (Pentester)** — ruxsat bilan tizimni buzish
- **Red Team** — haqiqiy hujumchi kabi harakat qilish
- **Bug Bounty Hunter** — kompaniyalar zaifligini topib mukofot olish

### 🔵 Defensive Security (Himoya tomoni)
Tizimlarni himoya qilish:
- **SOC Analyst** — xavfsizlik hodisalarini kuzatish
- **Blue Team** — hujumlarni aniqlash va to'xtatish
- **Digital Forensics** — jinoyat izlarini topish
- **Incident Response** — hujumdan keyin tiklash

### 🟣 Purple Team
Red va Blue team o'rtasidagi ko'prik — ikkalasini ham bilish

---

## 1.4 Qonuniy va noqonuniy hacking

> ⚠️ **DIQQAT**: Ruxsatsiz tizimga kirish — JINOYAT!

| Tur | Tavsif | Qonuniylik |
|-----|--------|-----------|
| **White Hat** | Ruxsat bilan buzadi, himoya uchun | ✅ Qonuniy |
| **Grey Hat** | Ba'zan ruxsatsiz, lekin zarar bermaydi | ⚠️ Cheklangan |
| **Black Hat** | Ruxsatsiz, shaxsiy manfaat uchun | ❌ Jinoyat |

Biz faqat **White Hat** yo'lini o'rganamiz!

---

## 1.5 Muhit o'rnatish

### Nima kerak?
- Kompyuter: kamida 8GB RAM, 50GB bo'sh disk
- Internet aloqa
- Sabr va qiziquvchanlik 😄

### Qadamlar:

---

### 1-QADAM: VirtualBox o'rnatish

VirtualBox — bu sizning kompyuteringizda "kompyuter ichida kompyuter" yaratadi. Kali Linux ni asosiy tizimingizga o'rnatmasdan xavfsiz ishlatish uchun kerak.

**Windows/Mac uchun:**
1. https://www.virtualbox.org/wiki/Downloads ga o'ting
2. Operatsion tizimingizga mos versiyani yuklab oling
3. O'rnatuvchini ishga tushiring, "Next" → "Next" → "Install"
4. O'rnatish tugagach, VirtualBox ni oching

---

### 2-QADAM: Kali Linux yuklab olish

Kali Linux — kiberxavfsizlik uchun maxsus yaratilgan operatsion tizim. Ichida 600+ hacking tool mavjud.

1. https://www.kali.org/get-kali/ ga o'ting
2. **"Virtual Machines"** bo'limini tanlang
3. **VirtualBox 64-bit** versiyasini yuklab oling (fayl .ova formatida, ~3GB)

---

### 3-QADAM: Kali Linux ni VirtualBox ga import qilish

1. VirtualBox ni oching
2. **File → Import Appliance** bosing
3. Yuklab olgan `.ova` faylni tanlang
4. **"Import"** bosing va kuting (~5-10 daqiqa)
5. Import tugagach, chap panelda **"Kali Linux"** ko'rinadi

---

### 4-QADAM: Kali Linux ni sozlash

Virtual mashinani boshlashdan oldin:

1. Kali Linux ni tanlang → **Settings** bosing
2. **System → Motherboard**: RAM ni 4096 MB ga o'rnating
3. **System → Processor**: 2 CPU ga o'rnating
4. **Display**: Video Memory ni 128 MB ga o'rnating
5. **OK** bosing

---

### 5-QADAM: Kali Linux ni ishga tushirish

1. **Start** tugmasini bosing
2. Login ekrani chiqadi:
   - Username: `kali`
   - Password: `kali`
3. Xush kelibsiz! 🎉

---

### 6-QADAM: Terminal ochish

Kali Linux da asosiy ish joyi — **Terminal** (qora ekran).

- Ekranning yuqori chap burchagidagi terminal ikonkasini bosing
- Yoki: `Ctrl + Alt + T`

Terminal ochilganda bunday ko'rinish bo'ladi:
```
┌──(kali㉿kali)-[~]
└─$
```

Bu `$` belgisi — siz oddiy foydalanuvchi sifatida ishlamoqdasiz.
`#` belgisi — siz root (administrator) sifatida ishlamoqdasiz.

---

### 7-QADAM: Birinchi buyruqlar

Terminal da quyidagilarni yozing:

```bash
# Tizim ma'lumotlari
uname -a

# Kim siz?
whoami

# Qayerdasiz?
pwd

# Fayllar ro'yxati
ls

# Internetga ulanishni tekshirish
ping google.com -c 4
```

---

### 8-QADAM: Kali Linux ni yangilash

Har doim yangi o'rnatgandan keyin tizimni yangilash kerak:

```bash
sudo apt update && sudo apt upgrade -y
```

Bu buyruq:
- `sudo` — administrator huquqi bilan ishlaish
- `apt update` — paketlar ro'yxatini yangilash
- `apt upgrade -y` — barcha dasturlarni yangilash
- `-y` — barcha savollarga "Ha" javob berish

Yangilash 10-20 daqiqa olishi mumkin. Kuting!

---

## 1.6 Muhim platformalar (hoziroq ro'yxatdan o'ting!)

| Platforma | Maqsad | Havola |
|-----------|--------|--------|
| **TryHackMe** | Boshlang'ichlar uchun, interaktiv | tryhackme.com |
| **HackTheBox** | O'rta/yuqori daraja | hackthebox.com |
| **PortSwigger** | Web xavfsizligi (bepul!) | portswigger.net/web-security |
| **PicoCTF** | CTF musobaqalar | picoctf.org |
| **VulnHub** | Zaif VM lar | vulnhub.com |

---

## 1.7 Qo'shimcha o'rnatish: Burp Suite

Burp Suite — web xavfsizligi uchun eng muhim tool.

```bash
# Kali da allaqachon o'rnatilgan, ishga tushirish:
burpsuite

# Yoki Applications → Web Application Analysis → burpsuite
```

---

## 1.8 Bob yakunida bilishingiz kerak bo'lgan narsalar

✅ Kiberxavfsizlik nima va nima uchun o'rganiladi  
✅ White/Grey/Black hat farqi  
✅ VirtualBox va Kali Linux o'rnatilgan  
✅ Terminal ochib oddiy buyruq yozish  
✅ Tizimni yangilash  

---

## 📝 Amaliy topshiriq #1

1. Kali Linux ni o'rnating va ishga tushiring
2. Terminalni oching
3. Quyidagi buyruqlarni bajaring va natijalarini daftaringizga yozing:
   ```bash
   whoami
   uname -a
   ip addr
   ls /home
   cat /etc/os-release
   ```
4. TryHackMe.com da ro'yxatdan o'ting va "Pre-Security" yo'lini boshlang

---

*Keyingi bob → 🌐 Tarmoq asoslari*
