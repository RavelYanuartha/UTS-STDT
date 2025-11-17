# Nama: Ravel Yanuartha 
# NIM: 215410134

---

## 1. Teorema CAP dan BASE

### A. Teorema CAP

Teorema CAP menyatakan bahwa dalam sistem terdistribusi kita hanya bisa mendapatkan dua dari tiga hal berikut secara bersamaan:
	•	Consistency (C) – semua node memiliki data yang sama pada waktu yang sama
	•	Availability (A) – sistem selalu merespons permintaan
	•	Partition Tolerance (P) – sistem tetap berjalan meski ada gangguan jaringan

Karena network partition pasti terjadi, maka sistem harus memilih antara:
	•	CP → konsisten, tapi kadang tidak tersedia
	•	AP → tersedia, tapi konsistensinya tertunda
---

### B. BASE

BASE adalah pendekatan sistem yang mengutamakan Availability (AP).
	•	Basically Available
	•	Soft-State
	•	Eventual Consistency

BASE digunakan oleh sistem yang memilih Availability + Partition Tolerance.

---
### C. Keterkaitan CAP & BASE
	•	Sistem AP → tidak bisa strong consistency
	•	BASE adalah solusi → memberi eventual consistency
	•	Jadi BASE adalah implementasi prinsip CAP pada sistem AP

---

### D. Contoh nyata

Saya pernah menggunakan MongoDB.
MongoDB termasuk sistem AP sehingga:
	•	lebih mengutamakan availability
	•	menggunakan mekanisme eventual consistency sesuai prinsip BASE

## 2. GraphQL dan Komunikasi Antar Proses (IPC)

### A. Penjelasan

Dalam sistem terdistribusi, banyak microservice berjalan terpisah (IPC).
GraphQL bisa menjadi gateway yang menghubungkan berbagai proses/microservice.

GraphQL:
	•	menerima 1 query dari client
	•	membuat request ke beberapa service (REST, gRPC, dll)
	•	menggabungkan jawabannya menjadi 1 response

Artinya GraphQL tidak menggantikan IPC, tapi mengkoordinasikan komunikasi antar proses.

### B.Diagram Komunikasi
graph TD

    '''mermaid
	C(Consistency)
    A(Availability)
    P(Partition<br>Tolerance)

    C -->|Memilih 2| CP[CP System]
    P --> CP
    A -->|Memilih 2| AP[AP System]
    P --> AP
    C -->|Memilih 2| CA[CA System (Tidak Mungkin Saat Partition)]
	'''
            +-----------------------+
            |        Client         |
            +-----------+-----------+
                        |
                        v
             +------------------------+
             |     GraphQL Gateway    |
             +--+---------+---------+-+
                |         |         |
                v         v         v
    +-----------+--+  +---+-----------+  +----------------+
    | Auth Service |  | Video Service |  |   User DB     |
    +--------------+  +---------------+  +----------------+
## 3.PostgreSQL Streaming Replication with Docker Compose

Dokumentasi ini menjelaskan langkah-langkah membuat *streaming replication* PostgreSQL menggunakan Docker dan Docker Compose. Replikasi ini memungkinkan satu server bertindak sebagai *Primary* dan server lain sebagai *Replica*, di mana Replica selalu mengikuti perubahan data dari Primary secara real-time.

---

## Tujuan
- Membangun cluster PostgreSQL dengan Primary dan Replica.
- Memahami alur sinkronisasi dengan mekanisme WAL (Write-Ahead Logging).
- Menguji proses replikasi secara sederhana.

---

## Langkah-Langkah Pembuatan

### 1. Siapkan struktur direktori
Buat folder project yang berisi folder untuk konfigurasi Primary, konfigurasi Replica, dan file Docker Compose.

### 2. Konfigurasi Primary Database
Atur primary agar mendukung streaming replication dengan mengaktifkan pengaturan replikasi dan WAL. Ini memungkinkan primary mengirim data perubahan ke replica.
### 3. Konfigurasi Replica Database
Aktifkan mode hot standby agar replica dapat mengikuti primary serta menerima query read-only.

### 4. Membuat dan Menjalankan Docker Compose
Siapkan file Docker Compose yang mendefinisikan dua service:
- *Primary*: sebagai sumber data.
- *Replica*: mengambil data awal dari primary dan mengikuti perubahan secara terus-menerus.

Kemudian jalankan seluruh service menggunakan Docker Compose.

### 5. Mengecek Status Replikasi
Periksa primary apakah replica berhasil terhubung dan berada dalam status streaming.  
Periksa juga replica apakah berjalan dalam mode recovery (standby).

### 6. Menguji Replikasi
Masukkan data baru ke primary, lalu cek di replica. Jika data muncul dengan otomatis, replikasi berhasil.

---

## Penjelasan Singkat Streaming Replication
Streaming replication bekerja dengan cara primary mengirimkan *WAL (Write-Ahead Log)* kepada replica.  
Setiap perubahan data yang dibuat di primary dicatat ke WAL, lalu dikirim ke replica untuk diterapkan ulang sehingga datanya tetap sinkron.

Keuntungan streaming replication:
- Data mirror real-time.
- Mendukung beban baca di replica.
- Menjadi dasar untuk sistem high availability dan failover.

---

## Catatan Tambahan
- Konfigurasi ini menggunakan *asynchronous replication*, sehingga replica dapat sedikit tertinggal dibanding primary.
- Bisa ditambahkan banyak replica dalam satu cluster.
- Mendukung pengembangan ke *synchronous replication* jika konsistensi absolut diperlukan.
