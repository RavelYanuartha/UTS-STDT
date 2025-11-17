Nama : Ravel Yanuartha
Nim : 215410134

1.	**Teorema CAP dan BASE**
**Teorema CAP (Consistency, Availability, Partition Tolerance)**
Teorema CAP, juga dikenal sebagai Teorema Brewer, adalah prinsip fundamental dalam sistem terdistribusi. Teorema ini menyatakan bahwa sistem penyimpanan data terdistribusi hanya dapat menjamin dua dari tiga properti berikut pada saat yang bersamaan:
- **Consistency (Konsistensi - C):** Setiap pembacaan akan mengembalikan data yang paling baru ditulis, atau error. Ini berarti semua node di sistem harus memiliki pandangan data yang sama pada saat yang bersamaan.
- **Availability (Ketersediaan - A):** Setiap permintaan (request) non-gagal akan menghasilkan respons (response) (tanpa menjamin bahwa data tersebut adalah yang paling baru), artinya sistem selalu up dan berfungsi.
- **Partition Tolerance (Toleransi Partisi - P):** Sistem terus beroperasi meskipun ada kegagalan komunikasi (network partition) antar node (misalnya, dua node tidak bisa "saling melihat" satu sama lain).

**Inti dari CAP:** Dalam lingkungan terdistribusi, network partition (P) adalah hal yang pasti terjadi. Ketika partisi terjadi, sistem harus memilih antara C dan A.
- **CP (Consistency and Partition Tolerance):** Jika Anda memilih C, sistem akan memblokir atau mengembalikan error pada request (mengorbankan A) sampai partisi diselesaikan dan konsistensi data dipastikan. Contoh: Basis data tradisional seperti MySQL (single-master) dan MongoDB sebelum versi 4.0 (default).
- **AP (Availability and Partition Tolerance):** Jika Anda memilih A, sistem akan terus menerima request baca/tulis (eventually mengorbankan C) karena node mungkin tidak memiliki data yang paling up-to-date akibat partisi. Contoh: Cassandra, Riak, dan DynamoDB.

**Teorema BASE (Basically Available, Soft state, Eventually consistent)**
BASE adalah filosofi desain yang muncul sebagai kebalikan dari sifat ACID (Atomicity, Consistency, Isolation, Durability) dan merupakan trade-off umum ketika Availability (A) dan Partition Tolerance (P) lebih diutamakan daripada Strong Consistency (C) (seperti pada sistem NoSQL).

- Basically Available (Ketersediaan Dasar): Sistem menjamin ketersediaan. Response terhadap permintaan dijamin, meskipun response tersebut mungkin menghasilkan data yang tidak konsisten atau sudah usang (stale). Ini berkorelasi langsung dengan A pada CAP.
- Soft state (Keadaan Lunak): Konsistensi data tidak diterapkan segera. Status data dapat berubah dari waktu ke waktu tanpa input eksternal karena Eventually Consistent.
- Eventually consistent (Konsisten Akhirnya): Data akan menjadi konsisten pada suatu titik di masa depan. Setelah tidak ada lagi pembaruan yang diterima, semua replika akan konvergen dan menjadi identik. Ini mengorbankan Strong Consistency (C) dari CAP.

**Keterkaitan CAP dan BASE**
BASE adalah implementasi dari trade-off AP pada Teorema CAP.
- Ketika sistem terdistribusi menghadapi network partition (P), sistem yang mengadopsi prinsip
- BASE akan memilih untuk tetap Available (A). Untuk mencapai A, sistem harus mengorbankan Strong Consistency (C), dan sebagai gantinya memilih Eventually Consistent (E pada BASE).
- CAP menjelaskan batasan mendasar dari sistem terdistribusi (Anda hanya bisa memilih dua).
- BASE menjelaskan strategi atau filosofi untuk membangun sistem yang beroperasi dalam batasan AP tersebut.

**Contoh Penggunaan Saya**
Saya pernah menggunakan Apache Cassandra untuk sebuah aplikasi log/metric berskala besar.
- **Mengapa Cassandra?** Karena membutuhkan ketersediaan tinggi (A) dan toleransi partisi (P). Jika satu node mati atau terpisah dari cluster, node lain harus tetap menerima data log.
- **Penerapan CAP:** Cassandra memilih AP.

- Jika terjadi partisi, request tulis ke node yang terpisah masih diterima (A).
- Node yang terpisah akan sinkron kembali setelah partisi sembuh (Eventually Consistent).

**Penerapan BASE:**

- Basically Available: Selama ada node yang hidup, kami bisa menulis log, memastikan service log tetap up.
- Eventually Consistent: Log yang ditulis ke satu node mungkin tidak langsung terlihat di node lain, tetapi kami tahu data tersebut akan akhirnya konsisten setelah replikasi selesai. Ini dapat ditoleransi karena data log/metrik tidak memerlukan konsistensi seketika seperti transaksi bank.

2.	**Keterkaitan GraphQL dengan Komunikasi Antar Proses pada Sistem Terdistribusi**
Dalam sistem terdistribusi (arsitektur microservices), IPC sering kali dilakukan menggunakan protokol berbasis HTTP, seperti REST. Masalah utama REST dalam skenario ini adalah over-fetching (mengambil data yang tidak diperlukan) atau under-fetching (memerlukan banyak request ke endpoint berbeda untuk mendapatkan semua data yang dibutuhkan).

**Peran GraphQL**
GraphQL adalah bahasa kueri untuk API dan runtime untuk memenuhi kueri tersebut dengan data yang ada. Dalam konteks sistem terdistribusi (terutama microservices), GraphQL berperan sebagai lapisan Gateway API atau API Aggregator yang sangat efisien.

- Efisiensi Data (Mengatasi Over/Under-fetching): Klien (misalnya, aplikasi frontend) hanya meminta data spesifik yang dibutuhkan. Server GraphQL hanya mengambil data yang diminta, mengurangi payload dan request yang tidak perlu di jaringan.
- Penyatuan Data (Data Aggregation): GraphQL memungkinkan request tunggal dari klien untuk mengakses data yang tersebar di beberapa microservice yang berbeda. Server GraphQL bertanggung jawab untuk memecah kueri tersebut, memanggil berbagai microservice (menggunakan resolver), menggabungkan hasilnya, dan mengembalikannya ke klien.
- Abstraksi Backend: Klien tidak perlu tahu bagaimana microservice dipecah atau di mana data disimpan. Mereka hanya berinteraksi dengan satu endpoint GraphQL dan model skema yang seragam.
