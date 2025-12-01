# Rancangan Aplikasi Pemesanan Katering

## 1. Rancangan Arsitektur Aplikasi

Aplikasi pemesanan katering ini akan menggunakan pendekatan arsitektur **microservices** atau **monorepo** tergantung pada kebutuhan skala. Saya merekomendasikan pendekatan **full-stack dengan pembagian backend dan frontend terpisah**.

### Arsitektur Umum:
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Backend        │    │    Database     │
│   (React)       │◄──►│   (Node.js/     │◄──►│    (PostgreSQL) │
│                 │    │   Express)       │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
        │                       │
        │                       │
        ▼                       ▼
┌─────────────────┐    ┌──────────────────┐
│   Admin Panel   │    │   Mobile App     │
│   (React)       │    │   (React Native) │
└─────────────────┘    └─────────────────┘
```

### Komponen Utama:
1. **Frontend Web** - Untuk customer memesan catering
2. **Admin Panel** - Untuk manajemen pesanan, menu, dan laporan
3. **API Backend** - Menyediakan layanan untuk frontend
4. **Database** - Menyimpan informasi pengguna, menu, pesanan, dll
5. **File Storage** - Untuk menyimpan gambar menu, logo, dll
6. **Payment Gateway** - Integrasi pembayaran online

## 2. Desain Database untuk Aplikasi

Berikut adalah skema database yang dirancang untuk aplikasi pemesanan katering:

### Tabel-tabel Utama:

1. **users**
   - id (UUID/Integer)
   - name (String)
   - email (String, unique)
   - password (String, hashed)
   - phone (String)
   - address (Text)
   - role (ENUM: customer, admin)
   - created_at (Timestamp)
   - updated_at (Timestamp)

2. **categories**
   - id (UUID/Integer)
   - name (String)
   - description (Text)
   - created_at (Timestamp)
   - updated_at (Timestamp)

3. **menus**
   - id (UUID/Integer)
   - name (String)
   - description (Text)
   - price (Decimal)
   - category_id (Foreign Key to categories)
   - image_url (String)
   - is_available (Boolean, default: true)
   - created_at (Timestamp)
   - updated_at (Timestamp)

4. **orders**
   - id (UUID/Integer)
   - user_id (Foreign Key to users)
   - total_amount (Decimal)
   - status (ENUM: pending, confirmed, preparing, delivered, cancelled)
   - delivery_date (Date)
   - delivery_time (Time)
   - delivery_address (Text)
   - notes (Text)
   - payment_method (ENUM: cash, online)
   - payment_status (ENUM: unpaid, paid, refunded)
   - created_at (Timestamp)
   - updated_at (Timestamp)

5. **order_items**
   - id (UUID/Integer)
   - order_id (Foreign Key to orders)
   - menu_id (Foreign Key to menus)
   - quantity (Integer)
   - price (Decimal)
   - subtotal (Decimal)

6. **payments**
   - id (UUID/Integer)
   - order_id (Foreign Key to orders)
   - amount (Decimal)
   - payment_method (String)
   - transaction_id (String)
   - status (ENUM: pending, success, failed, refunded)
   - created_at (Timestamp)
   - updated_at (Timestamp)

7. **catering_packages**
   - id (UUID/Integer)
   - name (String)
   - description (Text)
   - price (Decimal)
   - min_pax (Integer)
   - max_pax (Integer)
   - menu_ids (JSON/Array of menu IDs)
   - is_available (Boolean, default: true)
   - created_at (Timestamp)
   - updated_at (Timestamp)

## 3. Struktur Folder dan Teknologi yang Digunakan

### Teknologi yang Direkomendasikan:

**Backend:**
- Node.js dengan Express.js
- PostgreSQL sebagai database
- Sequelize atau Prisma sebagai ORM
- JWT untuk authentication
- Multer untuk upload file
- Stripe atau Midtrans untuk payment gateway
- Nodemailer untuk email notification

**Frontend:**
- React.js dengan TypeScript
- React Router untuk routing
- Redux atau Context API untuk state management
- Axios untuk API calls
- Tailwind CSS atau Material UI untuk styling
- React Hook Form untuk form handling

**Mobile:**
- React Native (jika ingin cross-platform)

### Struktur Folder:

```
catering-order-app/
├── backend/
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── utils/
│   ├── uploads/
│   ├── config/
│   ├── .env
│   ├── app.js
│   ├── server.js
│   └── package.json
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── context/
│   │   ├── hooks/
│   │   ├── utils/
│   │   ├── styles/
│   │   └── App.js
│   ├── package.json
│   └── tailwind.config.js
├── admin-panel/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── context/
│   │   ├── hooks/
│   │   ├── utils/
│   │   └── styles/
│   ├── package.json
│   └── tailwind.config.js
├── mobile/ (jika ada)
├── docker-compose.yml
├── README.md
└── .gitignore
```

## 4. Fitur-fitur Utama Aplikasi

### Fitur untuk Customer (Frontend Web):

1. **Authentication**
   - Registrasi dan login user
   - Lupa password
   - Verifikasi email

2. **Landing Page**
   - Tampilan awal dengan informasi perusahaan
   - Galeri menu
   - Testimoni pelanggan
   - Kontak informasi

3. **Pencarian dan Filter Menu**
   - Cari berdasarkan nama menu
   - Filter berdasarkan kategori
   - Filter berdasarkan harga

4. **Daftar Menu**
   - Tampilan grid/list menu
   - Detail menu (nama, deskripsi, harga, gambar)
   - Pilihan jumlah pesanan

5. **Keranjang Belanja**
   - Tambah/hapus item
   - Update jumlah item
   - Hitung total harga

6. **Proses Pemesanan**
   - Input data pemesan
   - Pilih tanggal dan waktu pengiriman
   - Input alamat pengiriman
   - Tambah catatan pesanan
   - Pilih metode pembayaran

7. **Riwayat Pesanan**
   - Daftar pesanan sebelumnya
   - Status pesanan
   - Detail pesanan
   - Cetak bukti pesanan

8. **Notifikasi**
   - Update status pesanan
   - Konfirmasi pembayaran

### Fitur untuk Admin (Admin Panel):

1. **Dashboard**
   - Ringkasan pesanan
   - Pendapatan harian/mingguan/bulanan
   - Statistik pelanggan

2. **Manajemen Menu**
   - Tambah/edit/hapus menu
   - Upload gambar menu
   - Atur harga dan ketersediaan

3. **Manajemen Kategori**
   - Tambah/edit/hapus kategori menu

4. **Manajemen Pesanan**
   - Lihat semua pesanan
   - Update status pesanan
   - Cetak invoice
   - Filter berdasarkan status

5. **Manajemen Customer**
   - Lihat daftar pelanggan
   - Lihat riwayat pesanan customer

6. **Laporan**
   - Laporan penjualan
   - Laporan pendapatan
   - Laporan menu terlaris

7. **Manajemen Paket Catering**
   - Buat paket katering
   - Atur harga dan kapasitas
   - Kelola isi paket

### Fitur Tambahan:

1. **Sistem Rating dan Review**
   - Customer dapat memberi rating dan ulasan
   - Tampilan rating di menu

2. **Integrasi Map**
   - Lokasi pengiriman di peta
   - Estimasi jarak dan waktu pengiriman

3. **Sistem Notifikasi**
   - Email dan SMS notification
   - Push notification (jika ada mobile app)

4. **Chat Support**
   - Live chat dengan customer service

5. **Promo dan Diskon**
   - Kode promo
   - Diskon khusus