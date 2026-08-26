# Web Server Log Intelligence

Dashboard monitoring dan analisis log Apache2 dan Nginx berbasis Python Standard Library.

Project ini dibuat sebagai command-line tool yang sederhana. Tidak membutuhkan Flask, pip, virtual environment, database, atau systemd.

Repository:

https://github.com/walidumar/mon-log-webserver-linux/

## Fitur

- Auto-detect Apache2
- Auto-detect Nginx
- Smart detection berdasarkan nama dan isi file log
- Support custom VHost log
- Support custom Server Block log
- Support access log
- Support error log
- Support log rotation `.1`, `.2`, `.gz`
- Total request atau hit
- HTTP 2xx, 3xx, 4xx, 5xx
- Total traffic
- Top IP Address
- Top URL
- Top User-Agent
- HTTP Method
- Request timeline
- Error monitoring
- Security detection
- Security Risk Score
- Dashboard web sementara
- Mode summary
- Mode detection
- Tidak berjalan sebagai daemon

## Requirements

Operating System:

- Ubuntu Server 22.04 LTS
- Ubuntu Server 24.04 LTS
- Debian 11
- Debian 12
- Debian 13
- Linux lain yang menyediakan Python 3.9+

Software:

- Python 3.9+
- Apache2 dan/atau Nginx jika ingin menganalisis web server tersebut

Tidak membutuhkan:

- Flask
- pip
- virtual environment
- MySQL
- MariaDB
- PostgreSQL
- Redis
- Docker
- systemd service

Semua library yang digunakan berasal dari Python Standard Library.

## Library Python

Script menggunakan library bawaan Python:

- `argparse`
- `collections`
- `datetime`
- `gzip`
- `glob`
- `html`
- `http.server`
- `json`
- `os`
- `re`
- `signal`
- `socket`
- `sys`
- `threading`
- `urllib`

Tidak perlu `pip install`.

## 1. Download Script

Clone repository:

```bash
git clone https://github.com/walidumar/mon-log-webserver-linux.git
cd mon-log-webserver-linux
```

Pastikan file utama bernama:

```text
mon-log
```

## 2. Berikan Permission Executable

Jalankan:

```bash
chmod +x mon-log
```

Periksa:

```bash
ls -lh mon-log
```

Harus terlihat permission executable, misalnya:

```text
-rwxr-xr-x
```

## 3. Jalankan dari Directory Project

Script dapat langsung dijalankan:

```bash
sudo ./mon-log
```

Tidak perlu:

```bash
python3 mon-log
```

Tidak perlu:

```bash
python mon-log
```

Tidak perlu:

```bash
pip install flask
```

Setelah dijalankan, dashboard tersedia di:

```text
http://127.0.0.1:8080
```

Tekan `CTRL+C` untuk menghentikan.

Script berjalan sebagai foreground process dan tidak membuat daemon atau systemd service.

## 4. Install sebagai Command Global

Agar command `mon-log` dapat digunakan dari directory mana pun, salin ke `/usr/local/bin`.

Jalankan:

```bash
sudo cp mon-log /usr/local/bin/mon-log
```

Pastikan executable:

```bash
sudo chmod +x /usr/local/bin/mon-log
```

Periksa:

```bash
which mon-log
```

Hasil yang diharapkan:

```text
/usr/local/bin/mon-log
```

Sekarang command dapat dipanggil dari mana saja:

```bash
sudo mon-log
```

## 5. Jalankan Dashboard

Command utama:

```bash
sudo mon-log
```

Output:

```text
Web Server Log Intelligence 1.0.0
========================================================================
Dashboard : http://127.0.0.1:8080
Hostname  : server01
Mode      : foreground
Refresh   : 15 seconds

Tekan CTRL+C untuk berhenti.
========================================================================
```

Buka browser:

```text
http://127.0.0.1:8080
```

Ketika terminal dihentikan dengan `CTRL+C`, dashboard juga berhenti.

## 6. Mode Summary

Jika hanya ingin melihat ringkasan tanpa menjalankan dashboard:

```bash
sudo mon-log --summary
```

Contoh:

```text
Web Server Log Intelligence 1.0.0
========================================================================
Host          : server01
Log files     : 12
Requests      : 125430
Errors        : 2810
Traffic       : 4.8 GB
Security      : MEDIUM (42/100)

HTTP 2xx      : 115210
HTTP 3xx      : 4200
HTTP 4xx      : 5400
HTTP 5xx      : 620

Top IP:
     12000  192.168.1.10
      9800  192.168.1.20

Security:
        12  Sensitive File Probe
         4  WordPress Scan

VHost:
  Apache2  walidumar             75200 requests  MEDIUM
  Apache2  sekolah               34200 requests  LOW
  Nginx    api                    16030 requests  NORMAL
```

Mode ini sangat berguna jika hanya ingin melakukan pemeriksaan cepat melalui SSH.

## 7. Mode Detect

Untuk melihat file log yang berhasil ditemukan:

```bash
sudo mon-log --detect
```

Contoh:

```text
Web Server Log Intelligence 1.0.0
========================================================================

[Apache2] /var/log/apache2

ACCESS walidumar              1.2 GB  /var/log/apache2/walidumar-access.log
ERROR  walidumar             85.0 MB  /var/log/apache2/walidumar-error.log
ACCESS sekolah              720.4 MB  /var/log/apache2/sekolah-access.log
ERROR  sekolah               42.1 MB  /var/log/apache2/sekolah-error.log

[Nginx] /var/log/nginx

ACCESS api                   340.2 MB  /var/log/nginx/api-access.log
ERROR  api                    12.4 MB  /var/log/nginx/api-error.log
```

Mode ini tidak menjalankan web server.

## 8. Menggunakan Port Berbeda

Default:

```text
8080
```

Jika port tersebut digunakan aplikasi lain:

```bash
sudo mon-log --port 9090
```

Dashboard:

```text
http://127.0.0.1:9090
```

## 9. Apache2 Default Log

Ubuntu dan Debian biasanya menggunakan:

```text
/var/log/apache2/access.log
/var/log/apache2/error.log
```

Script akan mencoba mendeteksi file tersebut secara otomatis.

## 10. Nginx Default Log

Nginx biasanya menggunakan:

```text
/var/log/nginx/access.log
/var/log/nginx/error.log
```

Script akan mencoba mendeteksi file tersebut secara otomatis.

## 11. Custom VirtualHost Apache2

Contoh:

```apache
<VirtualHost *:80>

    ServerName walidumar.example.com

    DocumentRoot /var/www/walidumar

    CustomLog /var/log/apache2/walidumar-access.log combined
    ErrorLog /var/log/apache2/walidumar-error.log

</VirtualHost>
```

Test:

```bash
sudo apachectl configtest
```

Reload:

```bash
sudo systemctl reload apache2
```

Kemudian:

```bash
sudo mon-log --detect
```

Script akan menemukan:

```text
/var/log/apache2/walidumar-access.log
/var/log/apache2/walidumar-error.log
```

dan mengelompokkannya sebagai:

```text
VHost: walidumar
```

## 12. Custom Server Block Nginx

Contoh:

```nginx
server {

    listen 80;

    server_name walidumar.example.com;

    root /var/www/walidumar;

    access_log /var/log/nginx/walidumar-access.log;
    error_log /var/log/nginx/walidumar-error.log;

}
```

Test:

```bash
sudo nginx -t
```

Reload:

```bash
sudo systemctl reload nginx
```

Kemudian:

```bash
sudo mon-log --detect
```

## 13. Custom Nama File Log

Script tidak hanya mengandalkan nama:

```text
access.log
error.log
```

Contoh file yang dapat dikenali:

```text
walidumar-access.log
walidumar-error.log

tokoku-access.log
tokoku-error.log

production-access.log
production-error.log

website.log
server-production.log
```

Untuk nama file yang tidak jelas, script mencoba membaca sampel isi file dan mencocokkannya dengan pola access log atau error log.

Contoh:

```text
walidumar.log
```

Jika isinya:

```text
192.168.1.10 - - [26/Aug/2026:19:40:12 +0800] "GET / HTTP/1.1" 200 1523 "-" "Mozilla/5.0"
```

maka file akan diklasifikasikan sebagai access log.

## 14. Log Rotation

File hasil logrotate juga didukung:

```text
access.log.1
access.log.2
access.log.3.gz

walidumar-access.log.1
walidumar-access.log.2.gz

walidumar-error.log.1
walidumar-error.log.2.gz
```

File `.gz` dibaca langsung menggunakan Python Standard Library.

## 15. Security Detection

Script mencari pola request yang mencurigakan.

Kategori yang tersedia:

```text
SQL Injection
Command Injection
Path Traversal
Sensitive File Probe
WordPress Scan
PHPMyAdmin Scan
Common Scanner
XSS Attempt
File Inclusion
Abnormal HTTP Method
Abnormally Long URL
```

Contoh:

```text
GET /../../etc/passwd HTTP/1.1
```

dapat dideteksi sebagai:

```text
Path Traversal
```

Contoh:

```text
GET /.env HTTP/1.1
```

dapat dideteksi sebagai:

```text
Sensitive File Probe
```

Security detection bersifat heuristic dan bukan bukti bahwa serangan berhasil.

## 16. Security Risk Score

Score berada pada rentang:

```text
0       NORMAL
1-29    LOW
30-59   MEDIUM
60-79   HIGH
80-100  CRITICAL
```

Score digunakan sebagai indikator awal untuk membantu administrator menentukan log atau VHost yang perlu diperiksa.

## 17. Informasi Dashboard

Dashboard menampilkan:

```text
Total Requests
HTTP 2xx
HTTP 3xx
HTTP 4xx
HTTP 5xx
Total Error
Total Traffic
Security Risk
Request Timeline
HTTP Status
HTTP Methods
Security Detection
Top IP
Top URL
Top User-Agent
Error Levels
Security Events
Detected Log Files
Frequent Error Messages
```

## 18. VirtualHost Monitoring

Jika satu server menjalankan beberapa website:

```text
Apache2
├── walidumar
├── sekolah
└── tokoku

Nginx
├── api
├── monitoring
└── portal
```

dashboard akan menampilkan statistik setiap VHost.

Contoh:

```text
walidumar
Requests : 75,200
Errors   : 1,240
Traffic  : 2.1 GB
Risk     : MEDIUM

sekolah
Requests : 34,200
Errors   : 420
Traffic  : 840 MB
Risk     : LOW
```

## 19. Permission Log

Log Apache2 dan Nginx biasanya tidak dapat dibaca oleh user biasa.

Jika muncul `Permission denied`, gunakan:

```bash
sudo mon-log
```

Periksa permission:

```bash
ls -lah /var/log/apache2/
ls -lah /var/log/nginx/
```

## 20. Tidak Menjadi Daemon

Project ini sengaja dibuat sebagai command utility.

Ketika menjalankan:

```bash
sudo mon-log
```

proses berjalan di terminal.

Ketika menekan:

```text
CTRL+C
```

proses berhenti.

Tidak dibuat:

- systemd service
- daemon
- cron job
- background worker permanen

Jika ingin melakukan pengecekan cepat:

```bash
sudo mon-log --summary
```

Jika ingin melihat file yang ditemukan:

```bash
sudo mon-log --detect
```

Jika ingin dashboard:

```bash
sudo mon-log
```

## 21. Uninstall

Jika command dipasang di `/usr/local/bin`:

```bash
sudo rm /usr/local/bin/mon-log
```

Tidak ada package manager atau service yang perlu dihapus.

## 22. Struktur Repository

```text
mon-log-webserver-linux/
│
├── mon-log
├── README.md
├── LICENSE
└── .gitignore
```

## 23. Kelebihan Model Command

Model ini sengaja dibuat sederhana:

```text
Copy
  ↓
chmod +x
  ↓
/usr/local/bin
  ↓
mon-log
```

Tidak perlu:

```text
Python command
Flask
pip
venv
requirements.txt
systemd
database
daemon
```

## 24. Roadmap

Pengembangan berikutnya dapat menambahkan:

- Incremental log parsing
- Cache hasil parsing
- GeoIP
- ASN detection
- Bot detection
- Brute-force detection
- Rate-limit detection
- Fail2Ban integration
- CrowdSec integration
- Telegram alert
- Email alert
- Prometheus metrics
- Grafana integration
- Multi-server monitoring
- Custom log format parser

## License

MIT License

Copyright (c) 2026 Walid Umar

## Repository

https://github.com/walidumar/mon-log-webserver-linux/

## Author

Walid Umar

Web Server Log Intelligence

Built with Python Standard Library.
