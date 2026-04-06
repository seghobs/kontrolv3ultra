# Kontrol v3 Ultra (Instagram Automation & Analysis System)

Kontrol v3 Ultra, gelişmiş bir Instagram Otomasyon, Yorum/Beğeni Kontrol ve Token Yönetim platformudur. Orijinal Instagram mobil uygulamasının (Android) gönderdiği API başlıklarını (header'ları), Bloks payload'larını ve session yönetimini birebir taklit ederek çalışan, oldukça kararlı ve güvenli bir sistemdir.

## 🚀 Proje Ne İşe Yarıyor?
Bu proje temelde, **Instagram yardımlaşma/etkileşim gruplarını yönetmek** ve üyelerin görevlerini tam olarak yapıp yapmadıklarını (beğeni/yorum atma) takip etmek amacıyla tasarlanmıştır.

* **Toplu Beğeni ve Yorum Denetimi:** Sisteme girilen gönderilerde, gruptaki kimin like atıp kimin atmadığını veya kimin yorum yapmadığını anında tespit eder. Detaylı eksik raporu sunar.
* **DM Grubu Entegrasyonu:** Sisteme eklenen temiz bir token sayesinde, hesabın bulunduğu Instagram mesaj (DM) gruplarını otomatik tespit eder ve o gruptaki kullanıcıların listesini ile son 24 saatte paylaşılan gönderileri filtreleyebilir.
* **Gelişmiş Filtreleme:** Sadece "Gönderi Paylaşanları" veya "90 Beğeninin Altındakileri" hedef alan akıllı UI toggle'ları mevcuttur. Backend veri yüklemeleri sırasında asenkron UI kilitlemesi yaparak güvenli bir form yönetimi sunar.
* **Güçlü Güvenlik ve Kimlik Doğrulama:** Python tabanlı API, Instagram'ın uyguladığı `403 (Çıkış Yaptın)`, `400 (Bad Request)` logiklerini Failover sistemiyle yönetir. Sadece gerçek oturum sonlanmalarında token'ları pasif eder, silinmiş/hatalı postlar yüzünden temiz token'ları yakmaz.
* **Dinamik Header İmparatorluğu:** Frida & Mitmproxy ile elde edilen native headerlar (`x-bloks-prism` vb.), `ig-intended-user-id` gibi parametrelerle donatılarak yakalanma (spam/scraping) ihtimalini minimize eder. Çoğul hesap desteği SQLite veri tabanı üzerinde çalışır.

---

## ✨ Genel Özellikler (Mevcut Veriler)
- **Flask Tabanlı Web Arayüzü:** Koyu tema (Dark Mode) ve esnek yapılı panel.
- **Gelişmiş Admin Yönetimi:** Admin panelinden token ekleme, güncelleme, silme veya manuel pasife alma özelliği.
- **Failover Mekanizmalı Token Doğrulama:** Instagram API'sinde oluşan anlık 400 ve 4xx/5xx hatalarını geçip yoluna devam etme, sadece kalıcı "Auth (401/403)" hatası aldığında token pasifleme özelliği.
- **Grup Analizi & Otomatizasyon:** Instagram DM grubundan tam üye çekme ve o gruba dün/bugün atılmış paylaşımları filtreleme.
- **Muaf Liste (Whitelist):** Genel muaf liste oluşturularak istenen kişilerin kontrollerden pas geçirilmesi.
- **Kendi Paylaşımını Tanıma:** Kullanıcının kendi paylaştığı postta doğal olarak "eksik" sayılmamasını sağlayan zeki kontrol mantığı.
- **GMT+3 (Türkiye) Saat Desteği:** Zaman formatlarının yerel saate göre çevrilmesi.

---

## 🛠️ Kurulum

Sistem tamamen Python ve Flask altyapısıyla çalışmaktadır. Sunucu (VPS/VDS) veya PythonAnywhere gibi platformlarda tam uyumludur.

### Tek Komutla Kurulum (Linux/Bash/PythonAnywhere)
```bash
bash -c "$(curl -sL https://raw.githubusercontent.com/seghobs/kontrolv3ultra/main/setup.sh)"
```

### Manuel Kurulum (Masaüstü & Geliştiriciler İçin)
```bash
git clone https://github.com/seghobs/kontrolv3ultra.git kontrol
cd kontrol
pip install -r requirements.txt
python flask_app.py
```

---

## 💻 Kullanım
Sunucu veya lokal makine üzerinde projeyi başlattıktan sonra aşağıdaki adreslerden erişim sağlayabilirsiniz:

**Ana Kontrol Paneli (Tarayıcı Açın):**
```text
http://localhost:5000
```
*(Birden çok link girebilir, ön belleğe alınan grup verilerinizi güvenle listeleyebilirsiniz.)*

**Admin Paneli (Token Yönetimi ve Muafiyetler):**
```text
http://localhost:5000/admin
```
*(Buradan token ekleyebilir, deaktif olanları "Tekrar Giriş Yap" veya onay sistemiyle aktif konuma geri çekebilirsiniz.)*

---

## 📁 Proje Yapısı

Kod mimarisi kolay geliştirilebilir modüller dizisinden oluşur:

- `flask_app.py`: Uygulama giriş noktası (Waitress veya Flask core üzerinden başlatır).
- `app_core/`: Sistemin belkemiği olan Backend ve Servis yapısı
  - `routes/`: Express/Flask route mantığı, backend UI köprüleri (`main.py`, `admin.py`).
  - `storage.py`: SQLite veritabanı (Tokenler, muafiyetler ve istatistik işlemleri).
  - `instagram_api.py`: Instagram'ın doğrudan Android v1 ve GraphQL altyapılarıyla veri çeken modül.
  - `session_state.py`: Token bazlı oturum takibi, `ig-u-ds-user-id` senkronizasyonlarının ve config bazlı session oluşturucuların (regex vs.) merkezi.
  - `config.py` & `token_service.py`: Sistemin otomatik rotasyon ayarlamalarını yapıp en az limitle karşılaşacağı hesabı (failover) sırayla atayan servisler.
  - `automation.py`: Arka planda çalışan zamanlanmış veritabanı / post süreçleri.
- `templates/`: Jinja2 altyapısıyla harmanlanmış duyarlı HTML dosyaları.
- `static/js/`: API etkileşimleri, asenkron "Loading" UI kilitlemeleri ve veri filtrelemeleri gibi işlemleri barındıran JavaScript dosyaları.
- `COKLU_TOKEN_README.md`: Projenin çoklu yetkilendirme dinamiklerinin ve Mitm proxy kullanımlarının dokümantasyonu.
