# 🍽️ Borcelle Fine Dining — Restaurant Reservation System

> Yapay zeka destekli, tam kapsamlı restoran yönetim ve rezervasyon platformu.

**Kocaeli Sağlık ve Teknoloji Üniversitesi**
Mühendislik ve Doğa Bilimleri Fakültesi — Bilgisayar/Yazılım Mühendisliği

| İsim | Öğrenci No | Görev |
|------|------------|-------|
| Simanur Gürsoy | 230502027 | Raporlama, Dokümantasyon, Frontend |
| Ebrar İkbal Karakuzu | 230502028 | Sunum, Görselleştirme,Test, Frontend |
| Alperen Yağmur | 250502015 | Backend, Frontend, Sistem Mimarisi |
| Utku Daşar | 240502061 | Proje Takibi |

**Ders Sorumlusu:** Dr. Öğr. Üyesi Elif Pınar Hacıbeyoğlu

---

## 📋 İçindekiler

1. [Proje Hakkında](#-proje-hakkında)
2. [Özellikler](#-özellikler)
3. [Mimari](#-sistem-mimarisi)
4. [Teknoloji Stack](#-teknoloji-stack)
5. [Kurulum](#-kurulum)
6. [Test Hesapları](#-test-hesapları)
7. [API Referansı](#-api-referansı)
8. [Proje Yapısı](#-proje-yapısı)
9. [Veritabanı Şeması](#-veritabanı-şeması)
10. [Kullanıcı Rolleri](#-kullanıcı-rolleri)
11. [Socket.IO Olayları](#-socketio-olayları)
12. [Ortam Değişkenleri](#-ortam-değişkenleri)
13. [Sorun Giderme](#-sorun-giderme)
14. [Lisans](#-lisans)

---

## 🎯 Proje Hakkında

Borcelle Fine Dining Reservation System, restoran operasyonlarındaki manuel yönetim zorluklarını çözmek amacıyla geliştirilmiş, yapay zeka entegreli bir full-stack web uygulamasıdır.

Sistem; müşteri portalı, yönetici paneli ve mutfak ekranından oluşan üç ayrı arayüzü gerçek zamanlı olarak birbirine bağlar. Müşteriler AI destekli chatbot aracılığıyla menü hakkında Türkçe sorular sorabilir, rezervasyon yapabilir; yöneticiler tek panelden tüm operasyonu takip edebilir; mutfak ekibi siparişleri anlık olarak görüp güncelleyebilir.

---

## ✨ Özellikler

### Müşteri Portalı (`localhost:7002`)
- 🔍 Menü görüntüleme ve kategori filtreleme
- 📅 Online rezervasyon oluşturma ve takip etme
- 🤖 Türkçe AI chatbot (LM Studio yerel LLM ile)
- 👤 Hesap oluşturma ve giriş yapma

### Admin Paneli (`localhost:7003`)
- 📊 Gerçek zamanlı dashboard istatistikleri
- 🍽️ Menü yönetimi (ekleme, düzenleme, silme, görsel yükleme)
- 🪑 Masa yönetimi ve doluluk takibi
- 📆 Takvim görünümlü rezervasyon yönetimi
- 📦 Sipariş yönetimi
- 👥 Kullanıcı ve müşteri yönetimi
- ⚙️ Restoran ayarları

### Mutfak Ekranı (`localhost:7004`)
- 📡 Socket.IO ile anlık sipariş bildirimleri
- ✅ Sipariş durumu güncelleme (bekliyor → hazırlanıyor → hazır → servis edildi)
- 🃏 Kart tabanlı sipariş görünümü

### Güvenlik & Altyapı
- 🔐 JWT tabanlı kimlik doğrulama (24 saat geçerli)
- 🛡️ Rol tabanlı erişim kontrolü (RBAC)
- 🔒 Bcrypt ile şifre hashleme
- 🚦 Rate limiting (SlowAPI)
- 🐳 Docker Compose ile tek komutlu kurulum

---

## 🏗️ Sistem Mimarisi

```
┌─────────────────────────────────────────────────────────┐
│                      İstemci Katmanı                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │ Customer App │  │ Admin Panel  │  │Kitchen Display│  │
│  │  :7002       │  │  :7003       │  │  :7004       │  │
│  │ React+TS+Vite│  │ React+TS+Vite│  │ React+TS+Vite│  │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  │
└─────────┼─────────────────┼─────────────────┼───────────┘
          │    HTTP/REST     │                 │ WebSocket
          ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────┐
│                   Backend Katmanı :7001                  │
│              FastAPI + Socket.IO + Uvicorn               │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐  │
│  │   Auth   │ │Reservations│ │   Menu  │ │  Orders  │  │
│  │  Router  │ │  Router  │ │  Router  │ │  Router  │  │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐               │
│  │  Tables  │ │  Admin   │ │  Upload  │               │
│  │  Router  │ │  Router  │ │  Router  │               │
│  └──────────┘ └──────────┘ └──────────┘               │
│              SQLAlchemy ORM + Alembic                    │
└─────────────────────────────┬───────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
┌──────────────────┐  ┌──────────────┐  ┌──────────────┐
│   PostgreSQL 15  │  │  LM Studio   │  │ Static Files │
│      :7005       │  │    :1234     │  │   (Images)   │
└──────────────────┘  └──────────────┘  └──────────────┘
```

---

## 🛠️ Teknoloji Stack

| Katman | Teknoloji | Versiyon |
|--------|-----------|----------|
| Backend Framework | FastAPI | 0.109.0 |
| ASGI Sunucu | Uvicorn | — |
| ORM | SQLAlchemy | — |
| DB Migration | Alembic | — |
| Veritabanı | PostgreSQL | 15 |
| Kimlik Doğrulama | JWT (python-jose) + Bcrypt | — |
| Gerçek Zamanlı | Socket.IO (python-socketio) | — |
| Rate Limiting | SlowAPI | — |
| Frontend | React + TypeScript | 18.2.0 / 5.3.3 |
| Build Aracı | Vite | 5.0.10 |
| Stil | TailwindCSS | 3.4.0 |
| HTTP İstemci | Axios | — |
| Grafik | Recharts | — |
| AI/LLM | LM Studio (Local) | — |
| Containerization | Docker + Docker Compose | 3.8 |

---

## 🚀 Kurulum

### Ön Gereksinimler

- **Docker & Docker Compose** (önerilen yöntem)
- veya **Python 3.11+**, **Node.js 18+**, **PostgreSQL 15+**

---

### Yöntem 1: Docker ile Kurulum (Önerilen)

```bash
# 1. Projeyi klonlayın
git clone https://github.com/Nereplaa/restaurant-reservation-system.git
cd restaurant-reservation-system

# 2. Tüm servisleri başlatın
docker-compose up --build
```

Yaklaşık 2-3 dakika bekleyin. Tüm servisler otomatik olarak başlar ve veritabanı seed edilir.

**Erişim adresleri:**

| Servis | URL |
|--------|-----|
| Müşteri Uygulaması | http://localhost:7002 |
| Admin Paneli | http://localhost:7003 |
| Mutfak Ekranı | http://localhost:7004 |
| API Dokümantasyonu | http://localhost:7001/api/docs |
| PostgreSQL | localhost:7005 |

---

### Yöntem 2: Manuel Kurulum

#### Backend

```bash
cd backend

# Virtual environment oluştur
python -m venv venv

# Aktive et
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Bağımlılıkları yükle
pip install -r requirements.txt

# Ortam değişkenlerini ayarla
cp .env.example .env
# .env dosyasını kendi konfigürasyonunuza göre düzenleyin

# Veritabanı migration'larını uygula
alembic upgrade head

# Örnek veri yükle (kullanıcılar, masalar, menü)
python seed.py

# Sunucuyu başlat
python run.py
```

Backend şu adreslerde erişilebilir olacak:
- API: `http://localhost:7001`
- Swagger UI: `http://localhost:7001/api/docs`
- Sağlık Kontrolü: `http://localhost:7001/health`

#### Frontend Uygulamaları

Her uygulama için aynı adımları tekrarlayın:

```bash
# Müşteri Uygulaması
cd frontend/customer-app
cp .env.example .env
npm install
npm run dev   # → http://localhost:5173 (geliştirme)

# Admin Paneli
cd frontend/admin-panel
cp .env.example .env
npm install
npm run dev

# Mutfak Ekranı
cd kitchen-display
cp .env.example .env
npm install
npm run dev
```

#### AI Chatbot (Opsiyonel)

1. [LM Studio](https://lmstudio.ai/) uygulamasını indirin ve kurun.
2. Tercih ettiğiniz bir modeli indirin (Türkçe destekli bir model önerilir).
3. LM Studio'yu `localhost:1234` portunda local server olarak başlatın.
4. `backend/.env` dosyasında LLM endpoint'ini yapılandırın.

---

## 👤 Test Hesapları

Veritabanı seed edildikten sonra aşağıdaki hesaplar kullanılabilir:

| Rol | E-posta | Şifre | Erişim |
|-----|---------|-------|--------|
| Admin | admin@restaurant.com | admin123 | Tam sistem erişimi |
| Yönetici | manager@restaurant.com | manager123 | Menü, masa, rezervasyon yönetimi |
| Servis | server@restaurant.com | server123 | Sipariş oluşturma, masa yönetimi |
| Mutfak | kitchen@restaurant.com | kitchen123 | Sipariş görüntüleme ve güncelleme |
| Müşteri | customer@example.com | customer123 | Rezervasyon ve menü |

---

## 📡 API Referansı

Tam interaktif dokümantasyon için: **`http://localhost:7001/api/docs`**

### Kimlik Doğrulama

```
POST   /api/v1/auth/register     Yeni kullanıcı kaydı
POST   /api/v1/auth/login        Giriş (JWT token döner)
GET    /api/v1/auth/me           Mevcut oturum bilgileri
POST   /api/v1/auth/logout       Çıkış
```

### Rezervasyonlar

```
GET    /api/v1/reservations          Rezervasyon listesi
POST   /api/v1/reservations          Yeni rezervasyon oluştur
GET    /api/v1/reservations/:id      Rezervasyon detayı
PATCH  /api/v1/reservations/:id      Güncelle
DELETE /api/v1/reservations/:id      Sil (admin/yönetici)
```

### Menü

```
GET    /api/v1/menu          Menü listesi (kategori filtresi destekler)
GET    /api/v1/menu/:id      Menü öğesi detayı
POST   /api/v1/menu          Yeni öğe ekle (admin/yönetici)
PATCH  /api/v1/menu/:id      Güncelle (admin/yönetici)
DELETE /api/v1/menu/:id      Sil (admin/yönetici)
```

### Masalar

```
GET    /api/v1/tables          Masa listesi (durum filtresi destekler)
GET    /api/v1/tables/:id      Masa detayı
POST   /api/v1/tables          Yeni masa (admin/yönetici)
PATCH  /api/v1/tables/:id      Güncelle
DELETE /api/v1/tables/:id      Sil (admin/yönetici)
```

### Siparişler

```
GET    /api/v1/orders                  Sipariş listesi (yalnızca personel)
POST   /api/v1/orders                  Yeni sipariş (admin/servis)
GET    /api/v1/orders/:id              Sipariş detayı
PATCH  /api/v1/orders/:id/status       Durum güncelle (admin/mutfak)
PATCH  /api/v1/orders/:id             Sipariş güncelle (admin/servis)
DELETE /api/v1/orders/:id             Sil (admin)
```

### Admin

```
GET    /api/v1/admin/stats     Dashboard istatistikleri (admin/yönetici)
GET    /api/v1/admin/users     Kullanıcı listesi (admin/yönetici)
```

### Sistem

```
GET    /health    Sağlık kontrolü
GET    /         API bilgisi
```

---

## 📁 Proje Yapısı

```
restaurant-reservation-system/
│
├── backend/                        # Python FastAPI Backend
│   ├── app/
│   │   ├── models/                 # SQLAlchemy veritabanı modelleri
│   │   │   ├── user.py
│   │   │   ├── table.py
│   │   │   ├── reservation.py
│   │   │   ├── menu_item.py
│   │   │   ├── order.py
│   │   │   ├── category.py
│   │   │   └── restaurant_settings.py
│   │   ├── routers/                # API endpoint'leri
│   │   │   ├── auth.py
│   │   │   ├── reservation.py
│   │   │   ├── menu.py
│   │   │   ├── order.py
│   │   │   ├── table.py
│   │   │   ├── category.py
│   │   │   ├── admin.py
│   │   │   ├── chat.py             # AI chatbot endpoint
│   │   │   ├── upload.py
│   │   │   └── settings.py
│   │   ├── schemas/                # Pydantic şemaları
│   │   ├── middleware/             # JWT auth middleware
│   │   ├── utils/                  # Yardımcı fonksiyonlar
│   │   ├── config.py
│   │   ├── database.py
│   │   └── main.py                 # Uygulama giriş noktası + Socket.IO
│   ├── alembic/                    # Veritabanı migration'ları
│   ├── requirements.txt
│   ├── seed.py                     # Örnek veri yükleme scripti
│   ├── run.py
│   ├── setup.sh / setup.bat
│   └── Dockerfile
│
├── frontend/
│   ├── admin-panel/                # Yönetici paneli (React + TS)
│   │   ├── src/
│   │   │   ├── components/         # ReservationCalendar, Sidebar vb.
│   │   │   ├── pages/              # Dashboard, Menu, Tables, Orders...
│   │   │   ├── services/           # API istemcileri
│   │   │   ├── contexts/           # AuthContext
│   │   │   └── types/
│   │   └── Dockerfile
│   │
│   └── customer-app/               # Müşteri uygulaması (React + TS)
│       ├── src/
│       │   ├── components/         # Navbar, Chatbot, FormComponents
│       │   ├── pages/              # Home, Menu, Booking, Reservations...
│       │   ├── services/
│       │   ├── contexts/
│       │   └── types/
│       └── Dockerfile
│
├── kitchen-display/                # Mutfak ekranı (React + TS)
│   ├── src/
│   │   ├── components/             # OrderCard
│   │   ├── services/               # socket.ts (Socket.IO istemci)
│   │   └── types/
│   └── Dockerfile
│
├── docker-compose.yml
├── README.md
├── QUICK-START.md
├── TECHNOLOGIES.md
├── CONTRIBUTING.md
└── LICENSE
```

---

## 🗄️ Veritabanı Şeması

### users
| Sütun | Tip | Açıklama |
|-------|-----|----------|
| id | UUID | Birincil anahtar |
| email | VARCHAR | Benzersiz, giriş için kullanılır |
| password_hash | VARCHAR | Bcrypt hash |
| role | ENUM | customer, server, kitchen, manager, admin |
| full_name | VARCHAR | — |
| created_at | TIMESTAMP | — |

### tables
| Sütun | Tip | Açıklama |
|-------|-----|----------|
| id | UUID | — |
| table_number | INTEGER | Masa numarası |
| capacity | INTEGER | Kaç kişilik |
| status | ENUM | available, occupied, reserved, maintenance |
| location | VARCHAR | İç mekan / dış mekan vb. |

### reservations
| Sütun | Tip | Açıklama |
|-------|-----|----------|
| id | UUID | — |
| confirmation_number | VARCHAR | Benzersiz onay kodu |
| user_id | UUID | FK → users |
| table_id | UUID | FK → tables |
| reservation_date | DATE | — |
| reservation_time | TIME | — |
| guest_count | INTEGER | Misafir sayısı |
| status | ENUM | confirmed, completed, cancelled, no_show |

### menu_items
| Sütun | Tip | Açıklama |
|-------|-----|----------|
| id | UUID | — |
| name | VARCHAR | Türkçe ad |
| category_id | UUID | FK → categories |
| price | DECIMAL | — |
| description | TEXT | — |
| image_url | VARCHAR | Görsel yolu |
| is_available | BOOLEAN | Stok durumu |

### orders
| Sütun | Tip | Açıklama |
|-------|-----|----------|
| id | UUID | — |
| table_id | UUID | FK → tables |
| status | ENUM | pending, preparing, ready, served, cancelled |
| total_amount | DECIMAL | — |

### order_items
| Sütun | Tip | Açıklama |
|-------|-----|----------|
| id | UUID | — |
| order_id | UUID | FK → orders |
| menu_item_id | UUID | FK → menu_items |
| quantity | INTEGER | — |
| unit_price | DECIMAL | Sipariş anındaki fiyat |

---

## 👥 Kullanıcı Rolleri

| Rol | Türkçe | Yetkiler |
|-----|--------|----------|
| `customer` | Müşteri | Rezervasyon oluşturma/görüntüleme, menü, chatbot |
| `server` | Servis | Sipariş oluşturma/güncelleme, masa durumu yönetimi |
| `kitchen` | Mutfak | Sipariş görüntüleme, hazırlık durumu güncelleme |
| `manager` | Yönetici | Menü, masa, rezervasyon yönetimi + kullanıcı listesi |
| `admin` | Admin | Tam sistem erişimi, kullanıcı yönetimi dahil |

---

## 📡 Socket.IO Olayları

### İstemciden Sunucuya

| Olay | Açıklama |
|------|----------|
| `connect` | Bağlantı kur |
| `join_kitchen` | Mutfak odasına katıl |
| `leave_kitchen` | Mutfak odasından ayrıl |
| `update_order_status` | Sipariş durumunu güncelle |

### Sunucudan İstemciye

| Olay | Açıklama |
|------|----------|
| `connection_established` | Bağlantı onayı |
| `joined_kitchen` | Mutfak odasına başarıyla katılındı |
| `new_order` | Yeni sipariş bildirimi |
| `order_updated` | Sipariş durumu değişti |
| `order_deleted` | Sipariş silindi |

---

## ⚙️ Ortam Değişkenleri

### Backend (`backend/.env`)

```env
# Sunucu
PORT=7001
HOST=0.0.0.0
ENVIRONMENT=development

# Veritabanı
DATABASE_URL=postgresql://postgres:postgres@localhost:7005/restaurant_db

# JWT
JWT_SECRET=your-super-secret-jwt-key-change-in-production
JWT_ALGORITHM=HS256
JWT_EXPIRATION_MINUTES=1440

# CORS (virgülle ayrılmış)
CORS_ORIGINS=http://localhost:7002,http://localhost:7003,http://localhost:7004

# Loglama
LOG_LEVEL=INFO
```

### Frontend (`frontend/customer-app/.env`)

```env
VITE_API_URL=http://localhost:7001/api/v1
VITE_SOCKET_URL=http://localhost:7001
```

---

## 🔧 Sorun Giderme

### Veritabanı bağlantısı kurulamıyor
```bash
# PostgreSQL çalışıyor mu kontrol et
docker ps | grep postgres

# .env dosyasındaki DATABASE_URL değerini doğrula
# Veritabanı mevcut mu kontrol et
createdb restaurant_db
```

### Port zaten kullanımda
`docker-compose.yml` veya `.env` dosyalarında ilgili portu değiştirin.

### Frontend backend'e ulaşamıyor
Frontend `.env` dosyasında `VITE_API_URL` adresini kontrol edin.

### Docker build hatası
```bash
docker-compose down -v
docker system prune -f
docker-compose up --build
```

### Migration hataları
```bash
alembic downgrade base
alembic upgrade head
```

### Import hatası
```bash
# Virtual environment aktif mi?
source venv/bin/activate  # macOS/Linux
venv\Scripts\activate     # Windows

pip install -r requirements.txt
```

---

## 📚 Ek Dokümantasyon

- [Hızlı Başlangıç Rehberi](QUICK-START.md)
- [Teknoloji Detayları](TECHNOLOGIES.md)
- [Backend README](backend/README.md)
- [Katkı Rehberi](CONTRIBUTING.md)
- [İnteraktif API Docs](http://localhost:7001/api/docs) *(uygulama çalışırken)*

---

## 📜 Lisans

MIT License — Detaylar için [LICENSE](LICENSE) dosyasına bakınız.

---

*Kocaeli Sağlık ve Teknoloji Üniversitesi — 2026*
