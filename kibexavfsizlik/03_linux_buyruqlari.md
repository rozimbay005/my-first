# 📘 3-BOB: LINUX BUYRUQLARI VA TIZIM BOSHQARUVI

---

## 3.1 Nima uchun Linux?

Kiberxavfsizlik mutaxassislarining 90%+ ishi Linux da o'tadi. Sabablari:
- 🆓 Bepul va ochiq kodli
- 🔧 Kuchli terminal va skriptlash imkoniyati
- 🛡️ Barcha hacking toollar Linux uchun yaratilgan
- 🌐 Serverlarning 96%+ Linux ishlatadi
- ⚙️ Tizim ustidan to'liq nazorat

---

## 3.2 Fayl tizimi tuzilmasi

Linux da hamma narsa fayl. Fayl tizimi `/` (root) dan boshlanadi:

```
/                   ← Root (asosiy papka)
├── bin/            ← Asosiy buyruqlar (ls, cp, mv...)
├── boot/           ← Yuklash fayllari (kernel)
├── dev/            ← Qurilmalar (disk, USB...)
├── etc/            ← Konfiguratsiya fayllari
├── home/           ← Foydalanuvchilar papkalari
│   └── kali/       ← Kali foydalanuvchisi
├── lib/            ← Kutubxonalar
├── media/          ← USB, CD mount joyi
├── mnt/            ← Vaqtincha mount
├── opt/            ← Qo'shimcha dasturlar
├── proc/           ← Jarayonlar ma'lumoti (virtual)
├── root/           ← Root foydalanuvchi papkasi
├── sbin/           ← Tizim buyruqlari (admin uchun)
├── tmp/            ← Vaqtincha fayllar
├── usr/            ← Foydalanuvchi dasturlari
│   ├── bin/        ← Ko'pchilik buyruqlar
│   └── share/      ← Umumiy ma'lumotlar
└── var/            ← O'zgaruvchan fayllar (loglar, bazalar)
    └── log/        ← Log fayllar
```

**Muhim papkalar:**

| Papka | Nima uchun muhim? |
|-------|-------------------|
| `/etc/passwd` | Foydalanuvchilar ro'yxati |
| `/etc/shadow` | Shifrlangan parollar |
| `/etc/hosts` | Lokal DNS |
| `/var/log/` | Barcha loglar |
| `/tmp/` | Vaqtincha fayllar (hujumchilar ko'p ishlatadi) |
| `/root/` | Root foydalanuvchi uyi |
| `/home/` | Oddiy foydalanuvchilar |

---

## 3.3 Navigatsiya buyruqlari

```bash
# Hozirgi joylashuvni ko'rish
pwd
# Natija: /home/kali

# Papkaga kirish
cd /etc
cd ~          # Home papkaga qaytish
cd ..         # Bir yuqoriga
cd ../..      # Ikki yuqoriga
cd -          # Oldingi papkaga qaytish

# Papka tarkibini ko'rish
ls            # Oddiy ro'yxat
ls -l         # Batafsil (huquqlar, hajm, sana)
ls -a         # Yashirin fayllar ham
ls -la        # Ikkalasi ham
ls -lh        # Hajmni o'qish qulay formatda (KB, MB)
ls -lt        # Sana bo'yicha tartiblash
ls /etc       # Boshqa papkani ko'rish

# Natija:
# drwxr-xr-x  2 root root 4096 Jan 15 10:30 network
# -rw-r--r--  1 root root  220 Jan 15 10:30 passwd
# d = papka, - = fayl, l = havola
```

---

## 3.4 Fayl va papka boshqaruvi

```bash
# Papka yaratish
mkdir mening_papkam
mkdir -p a/b/c        # Ichma-ich papkalar yaratish

# Bo'sh fayl yaratish
touch fayl.txt
touch fayl1.txt fayl2.txt fayl3.txt   # Bir vaqtda ko'p fayl

# Fayl nusxalash
cp fayl.txt nusxa.txt
cp -r papka/ yangi_papka/   # Papkani nusxalash (-r = recursive)

# Fayl ko'chirish / nomini o'zgartirish
mv fayl.txt boshqa_joy/fayl.txt
mv eski_nom.txt yangi_nom.txt   # Nomini o'zgartirish

# O'chirish
rm fayl.txt
rm -r papka/          # Papkani o'chirish
rm -rf papka/         # Kuchliroq o'chirish (diqqat!)
rmdir bo'sh_papka/    # Faqat bo'sh papkani o'chirish

# Fayl topish
find / -name "parol.txt"          # Butun tizimda qidirish
find /home -name "*.txt"          # Home da .txt fayllar
find / -name "passwd" 2>/dev/null # Xatolarni ko'rsatmaslik
find / -perm /4000 2>/dev/null    # SUID fayllar (muhim!)
find / -type f -size +10M         # 10MB dan katta fayllar
find /tmp -mtime -1               # 1 kun ichida o'zgargan

# Fayl qidirish (tezroq)
locate passwd
updatedb    # locate ma'lumotlar bazasini yangilash

# Buyruq qayerda?
which nmap
whereis python3
```

---

## 3.5 Fayl mazmunini o'qish

```bash
# To'liq faylni o'qish
cat /etc/passwd
cat fayl.txt

# Sahifalab o'qish (katta fayllar uchun)
less /var/log/syslog    # q = chiqish, / = qidirish
more fayl.txt

# Faqat boshini ko'rish
head fayl.txt           # Birinchi 10 satr
head -n 20 fayl.txt     # Birinchi 20 satr

# Faqat oxirini ko'rish
tail fayl.txt           # Oxirgi 10 satr
tail -n 50 fayl.txt     # Oxirgi 50 satr
tail -f /var/log/syslog # Real vaqtda kuzatish (log uchun!)

# Fayl haqida ma'lumot
file fayl.txt     # Fayl turi
wc -l fayl.txt    # Satrlar soni
wc -w fayl.txt    # So'zlar soni
du -sh papka/     # Papka hajmi
```

---

## 3.6 Matn qidirish — grep

`grep` — fayllar ichidan matn qidirish. Kiberxavfsizlikda juda ko'p ishlatiladi!

```bash
# Asosiy qidirish
grep "root" /etc/passwd
grep "error" /var/log/syslog

# Katta-kichik harfga e'tibor bermaslik
grep -i "password" fayl.txt

# Qarama-qarshi (topilmaganlarni ko'rish)
grep -v "nologin" /etc/passwd

# Qator raqami bilan
grep -n "root" /etc/passwd

# Recursive (papka ichida)
grep -r "password" /etc/

# Faqat fayl nomini ko'rsatish
grep -l "admin" /etc/*

# Regular expression bilan
grep -E "root|admin" /etc/passwd

# Kontekst bilan (atrofdagi satrlar)
grep -A 2 "error" log.txt    # 2 satr keyin
grep -B 2 "error" log.txt    # 2 satr oldin
grep -C 2 "error" log.txt    # Ikkalasi

# Parollarni qidirish (amaliyot uchun)
grep -r "password" /var/www/ 2>/dev/null
grep -r "passwd" /etc/ 2>/dev/null
```

---

## 3.7 Pipe va Redirect

Pipe (`|`) — bir buyruq natijasini boshqasiga uzatish.
Redirect (`>`, `>>`) — natijani faylga yozish.

```bash
# PIPE misollari
cat /etc/passwd | grep "bash"        # passwd dan bash foydalanuvchilar
ls -la | grep "rwxr"                 # Huquqli fayllar
ps aux | grep "apache"               # Apache jarayonlari
cat /etc/passwd | cut -d: -f1        # Faqat foydalanuvchi nomlari
cat log.txt | sort | uniq            # Tartibla va takroriylarni olib tashlash
cat log.txt | wc -l                  # Satrlar soni

# REDIRECT misollari
echo "Salom" > fayl.txt             # Yozish (o'chiradi)
echo "Dunyo" >> fayl.txt            # Qo'shish (o'chirmaydi)
ls -la > natija.txt                  # Natijani faylga saqlash
nmap 192.168.1.1 > skan.txt          # Nmap natijasini saqlash

# Xatolarni yo'q qilish
find / -name "test" 2>/dev/null      # Xatolarni ko'rsatma
find / -name "test" 2>&1 | grep -v "denied"  # Xatolarni filtrla

# Stdin dan o'qish
cat < fayl.txt
```

---

## 3.8 Foydalanuvchilar va huquqlar

### Foydalanuvchilar

```bash
# Kim siz?
whoami

# Barcha foydalanuvchilar
cat /etc/passwd
# Format: username:x:UID:GID:info:home:shell

# Guruhlar
cat /etc/group
id             # O'z UID, GID va guruhlar
id root        # Root ning UID va GID

# Foydalanuvchi qo'shish
sudo useradd -m yangi_user       # -m = home papka yaratish
sudo useradd -m -s /bin/bash ali # bash shell bilan

# Parol o'rnatish
sudo passwd ali

# Foydalanuvchi o'chirish
sudo userdel -r ali    # -r = home papkasini ham o'chirish

# Guruhga qo'shish
sudo usermod -aG sudo ali    # ali ni sudo guruhiga qo'shish

# Foydalanuvchiga aylantirish
su ali           # ali ga o'tish
su -             # root ga o'tish
sudo -i          # Root shell ochish
```

### Huquqlar tizimi

```bash
ls -la
# -rwxr-xr--  1  kali  kali  1234  Jan 15  fayl.txt
#  |||||||||||
#  |--------| → Huquqlar
#  | → Fayl turi: - = fayl, d = papka, l = link

# Huquqlar 3x3 guruhda:
# rwx r-x r--
# ||| ||| |||
# ||| ||| └── Other (boshqalar): o'qish (r)
# ||| └────── Group (guruh): o'qish + ishlatish (r-x)
# └────────── Owner (egasi): hammasi (rwx)

# r = read    = 4
# w = write   = 2
# x = execute = 1
# - = yo'q    = 0

# rwx = 4+2+1 = 7
# r-x = 4+0+1 = 5
# r-- = 4+0+0 = 4
# rw- = 4+2+0 = 6
```

**chmod — huquqlarni o'zgartirish:**

```bash
# Raqamli usul
chmod 777 fayl.txt    # Hammaga hammasi (rwxrwxrwx)
chmod 755 fayl.txt    # Egaga hammasi, boshqalarga o'qish+ishlatish
chmod 644 fayl.txt    # Egaga o'qish+yozish, boshqalarga faqat o'qish
chmod 600 fayl.txt    # Faqat egaga o'qish+yozish
chmod 400 fayl.txt    # Faqat egaga o'qish

# Harfli usul
chmod +x fayl.txt     # Hammaga execute huquqi
chmod -w fayl.txt     # Yozish huquqini olish
chmod u+x fayl.txt    # Faqat egaga execute
chmod g-w fayl.txt    # Guruhdan yozish huquqini olish
chmod o-r fayl.txt    # Boshqalardan o'qish huquqini olish

# Papkaga recursive
chmod -R 755 papka/
```

**chown — egasini o'zgartirish:**

```bash
sudo chown ali fayl.txt          # Egasini ali ga o'zgartirish
sudo chown ali:developers fayl   # Egasi va guruhi
sudo chown -R www-data /var/www/ # Recursive
```

### SUID, SGID, Sticky Bit (Muhim — Privilege Escalation uchun!)

```bash
# SUID bit — fayl egasi huquqida ishlatiladi
# Misol: /usr/bin/passwd root huquqida ishlaydi
chmod u+s fayl
chmod 4755 fayl

# SUID fayllarni topish (zaiflik qidirish)
find / -perm -4000 2>/dev/null
find / -perm /4000 -type f 2>/dev/null

# SGID bit
chmod g+s fayl
chmod 2755 fayl

# Sticky bit — /tmp papkasida ishlatiladi
chmod +t /tmp
chmod 1777 /tmp
```

---

## 3.9 Jarayonlar boshqaruvi

```bash
# Ishlaydigan jarayonlarni ko'rish
ps aux
ps aux | grep apache    # Bitta jarayonni qidirish

# Real vaqtda kuzatish
top         # Dinamik ko'rinish
htop        # Chiroyli ko'rinish (sudo apt install htop)

# Jarayon o'ldirish
kill 1234           # PID bo'yicha to'xtatish
kill -9 1234        # Kuchli to'xtatish (SIGKILL)
killall apache2     # Nomi bo'yicha

# Fon rejimida ishlatish
nmap -p- 192.168.1.1 &          # & = fon rejimi
nmap -p- 192.168.1.1 & disown   # Terminaldan ajratish

# Jarayonlar
jobs               # Fon jarayonlar
fg 1               # Oldingi plandga chiqarish
bg 1               # Fon rejimiga yuborish

# Jarayonni vaqtinchalik to'xtatish
Ctrl + Z           # To'xtatish
fg                 # Davom ettirish

# Nohush chiqishdan keyin davom etish
nohup buyruq &     # Terminal yopilsa ham davom etadi
screen             # Virtual terminal (kuchli tool)
tmux               # Yanada kuchliroq
```

---

## 3.10 Tarmoq buyruqlari

```bash
# IP manzillar
ip addr show
ip addr show eth0   # Bitta interfeys
ifconfig            # Eski usul

# Tarmoq ulanishlari
ip route show       # Routing jadval
route -n            # Eski usul
netstat -tulpn      # Ochiq portlar va ulanishlar
ss -tulpn           # Yangi usul (tezroq)

# Ochiq portlarni ko'rish
netstat -tulpn | grep LISTEN
ss -tulpn | grep LISTEN

# Faol ulanishlar
netstat -an
ss -an

# Tarmoq interfeysini o'chirish/yoqish
sudo ip link set eth0 down
sudo ip link set eth0 up

# IP manzil o'rnatish
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip addr del 192.168.1.100/24 dev eth0
```

---

## 3.11 Paketlar boshqaruvi (APT)

```bash
# Ro'yxatni yangilash
sudo apt update

# Dasturlarni yangilash
sudo apt upgrade -y

# Dastur o'rnatish
sudo apt install nmap
sudo apt install wireshark burpsuite metasploit-framework

# Dastur o'chirish
sudo apt remove nmap
sudo apt purge nmap      # Konfiguratsiya bilan birga

# Qidirish
apt search "port scanner"
apt show nmap            # Ma'lumot

# Kesh tozalash
sudo apt autoremove
sudo apt autoclean
```

---

## 3.12 Muhim konfiguratsiya fayllari

```bash
# Foydalanuvchilar
cat /etc/passwd          # Foydalanuvchilar (parol yo'q)
sudo cat /etc/shadow     # Shifrlangan parollar (root kerak!)
cat /etc/group           # Guruhlar

# Tarmoq
cat /etc/hosts           # Lokal DNS
cat /etc/resolv.conf     # DNS serverlar
cat /etc/network/interfaces  # Tarmoq interfeyslari
cat /etc/hostname        # Kompyuter nomi

# SSH
cat /etc/ssh/sshd_config  # SSH server konfiguratsiyasi

# Cron (avtomatik vazifalar)
crontab -l               # O'z cron vazifalari
sudo crontab -l          # Root cron
cat /etc/crontab         # Tizim cron
ls /etc/cron.d/          # Cron papkasi

# Bash konfiguratsiya
cat ~/.bashrc            # Bash sozlamalari
cat ~/.bash_history      # Buyruqlar tarixi (parollar bo'lishi mumkin!)
cat /root/.bash_history  # Root tarixi
```

---

## 3.13 Log fayllar

Loglar hujumni aniqlash va forensics uchun juda muhim!

```bash
# Asosiy loglar
tail -f /var/log/syslog          # Tizim logi (real vaqt)
tail -f /var/log/auth.log        # Autentifikatsiya logi (SSH kirish!)
cat /var/log/kern.log            # Kernel logi
cat /var/log/dpkg.log            # O'rnatilgan paketlar

# Apache/Nginx loglar
tail -f /var/log/apache2/access.log   # Veb kirish logi
tail -f /var/log/apache2/error.log    # Xatolar
tail -f /var/log/nginx/access.log

# SSH muvaffaqiyatsiz urinishlar
grep "Failed password" /var/log/auth.log
grep "Invalid user" /var/log/auth.log

# Muvaffaqiyatli SSH kirishlar
grep "Accepted" /var/log/auth.log

# Sudo ishlatish tarixi
grep "sudo" /var/log/auth.log
```

---

## 3.14 Bash skriptlash asoslari

```bash
#!/bin/bash
# Bu birinchi skript!

# O'zgaruvchilar
ISM="Kali"
YOSH=25
echo "Salom, $ISM! Yoshingiz: $YOSH"

# Input olish
echo "Ismingizni kiriting:"
read FOYDALANUVCHI
echo "Salom, $FOYDALANUVCHI!"

# Shartlar
if [ $YOSH -ge 18 ]; then
    echo "Kattakor"
elif [ $YOSH -ge 13 ]; then
    echo "O'smir"
else
    echo "Bola"
fi

# Tsikl
for i in 1 2 3 4 5; do
    echo "Raqam: $i"
done

# Massiv
PORTLAR=(22 80 443 3306 8080)
for PORT in "${PORTLAR[@]}"; do
    echo "Port: $PORT tekshirilmoqda"
done

# Funksiya
skanla() {
    echo "$1 skanlanmoqda..."
    nmap -sV $1
}
skanla 192.168.1.1

# Faylni saqlash va ishlatish
chmod +x skript.sh
./skript.sh
```

**Amaliy skript — tarmoq skaneri:**
```bash
#!/bin/bash
# ping_sweep.sh — Tarmoqdagi tirik qurilmalarni topish

TARMOQ="192.168.1"
echo "Tarmoq skanlanmoqda: $TARMOQ.0/24"
echo "================================"

for i in $(seq 1 254); do
    ping -c 1 -W 1 $TARMOQ.$i > /dev/null 2>&1
    if [ $? -eq 0 ]; then
        echo "[+] $TARMOQ.$i — TIRIK"
    fi
done

echo "Skan tugadi!"
```

---

## 3.15 Muhim qisqa buyruqlar ro'yxati

```bash
# Tizim ma'lumoti
uname -a          # Kernel versiyasi
hostname          # Kompyuter nomi
uptime            # Qancha vaqt ishlayapti
date              # Sana va vaqt
df -h             # Disk bo'sh joy
free -h           # RAM holati
lscpu             # CPU ma'lumoti
lsblk             # Disklar

# Arxivlash
tar -czf arxiv.tar.gz papka/    # Siqish
tar -xzf arxiv.tar.gz           # Ochish
zip -r arxiv.zip papka/         # ZIP
unzip arxiv.zip                 # ZIP ochish

# Fayl yuklab olish
wget https://example.com/fayl.txt
curl -O https://example.com/fayl.txt
curl -s https://api.example.com/data    # API so'rov

# Matn tahrirlash
nano fayl.txt     # Oddiy editor (Ctrl+X = chiqish)
vim fayl.txt      # Kuchli editor (:q! = chiqish, :wq = saqlash)

# Parol generatsiya
openssl rand -base64 16    # 16 ta random belgi
```

---

## 3.16 Bob yakunida bilishingiz kerak

✅ Linux fayl tizimi tuzilmasi  
✅ Navigatsiya: `cd`, `ls`, `pwd`  
✅ Fayl boshqaruvi: `cp`, `mv`, `rm`, `find`  
✅ Matn o'qish: `cat`, `grep`, `less`, `tail -f`  
✅ Pipe (`|`) va redirect (`>`, `>>`)  
✅ Huquqlar: `chmod`, `chown`, SUID  
✅ Jarayonlar: `ps`, `kill`, `top`  
✅ Log fayllarni o'qish  
✅ Bash skript yozish  

---

## 📝 Amaliy topshiriq #3

```bash
# 1. Barcha SUID fayllarni toping
find / -perm -4000 2>/dev/null

# 2. Parol bo'lishi mumkin bo'lgan fayllarni qidiring
grep -r "password" /etc/ 2>/dev/null | head -20

# 3. SSH urinishlarini ko'ring
grep "Failed" /var/log/auth.log 2>/dev/null | tail -20

# 4. Ping sweep skriptini yozing va ishga tushiring
# (yuqoridagi ping_sweep.sh dan foydalaning)

# 5. Quyidagi ma'lumotlarni bitta faylga saqlang:
(
  echo "=== TIZIM ==="
  uname -a
  echo "=== FOYDALANUVCHILAR ==="
  cat /etc/passwd | grep bash
  echo "=== OCHIQ PORTLAR ==="
  ss -tulpn
  echo "=== TARMOQ ==="
  ip addr show
) > tizim_ma'lumot.txt
cat tizim_ma'lumot.txt
```

---

*Keyingi bob → 🔐 Kriptografiya asoslari*
