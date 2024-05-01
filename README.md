# Tutorial 9 - Advanced Programming - High Level Networking
**Akmal Ramadhan - 2206081534 - Kelas A**

## Refleksi

### Nomor 1
> What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

| **Jenis RPC**                | **Deskripsi**                                                                                                     | **Kelebihan**                                                           | **Kekurangan**                                                       | **Contoh Penggunaan**                                               |
|------------------------------|-------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|-----------------------------------------------------------------------|---------------------------------------------------------------------|
| **Unary RPC**                | Client mengirim satu request dan menerima satu response dari server.                                              | Sederhana dan mudah diimplementasikan.                                  | Tidak efisien untuk transfer data besar atau kontinu.                 | Mengambil data spesifik, autentikasi user.                          |
| **Server Streaming RPC**     | Server mengirimkan serangkaian response ke client sebagai response terhadap satu request.                         | Cocok untuk mengirim data besar atau streaming data kontinu.            | Client hanya dapat mengirim request sekali di awal.                   | Streaming data pasar saham, pembaruan cuaca, video streaming.        |
| **Client Streaming RPC**     | Client mengirim serangkaian request ke server yang kemudian mengirimkan satu response setelah semua pesan diterima. | Memungkinkan client untuk terus mengirim data dalam sesi yang sama.     | Server menunggu semua data diterima sebelum mengirim response.        | Mengirim data dalam jumlah besar secara bertahap, seperti upload file besar. |
| **Bidirectional Streaming RPC** | Client dan server dapat saling mengirim serangkaian pesan secara bergantian.                                       | Memungkinkan komunikasi dua arah yang interaktif dan real-time.         | Lebih kompleks dalam implementasi dan pengelolaan.                   | Aplikasi chat, analitik real-time, permainan interaktif.             |

### Nomor 2
> What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Pertama, kita harus pastikan koneksi antara _client_ dan _server_ aman. Hal ini bisa dilakukan dengan menggunakan TLS yang membuat data terenkripsi saat berpindah antar _client_ dan _server_. Kedua, kita perlu tahu pasti siapa yang mengakses layanan tersebut. Hal ini bisa dilakukan dengan memberikan token khusus pada _client_, seperti token JWT. Ketiga, kita harus pastikan bahwa pengguna hanya bisa melakukan hal yang diperbolehkan. Misalnya, jika seseorang adalah pengguna biasa, dia tidak bisa melakukan hal yang hanya bisa dilakukan oleh admin. Terakhir, kita harus memastikan data yang dikirim antar _client_ dan _server_ tetap aman. Ini berarti data harus dienkripsi saat transit. Dengan memperhatikan hal-hal ini, kita bisa membuat layanan gRPC kita jadi lebih aman.

### Nomor 3
> What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Tantangannya yaitu kita harus memastikan pesan dari _client_ dan _server_ yang sedang diproses tidak saling bertabrakan atau sinkron sehingga tidak ada pesan yang tercampur atau terputus saat transit. Pikirkan juga metode _buffering_ yang digunakan. Semakin banyak _buffer_, pesan memiliki penundaan yang sangat lama. Semakin sedikit _buffer_, konsumsi daya _server_ semakin besar pula. Setelah itu, kita juga harus dapat mengelola koneksi dari banyak _client_ yang berbeda. Aplikasi obrolan yang sibuk bisa jadi membebani _server_. Terakhir, pastikan dapat menangani kesalahan koneksi akibat dari putus koneksi yang mendadak. Hal ini penting untuk mencegah penyadapan atau manipulasi data saat transit. 

### Nomor 4
> What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?

**Kelebihan:**
- Fleksibel karena `ReceiverStream` dapat mengonversi `tokio::sync::mpsc::Receiver` menjadi stream yang dapat digunakan dalam implementasi gRPC.
- Penggunaan `ReceiverStream` mudah.
- `ReceiverStream` dapat diintegrasikan dengan fitur dan alat lain dari `tokio`.

**Kekurangan:**
- Implementasi `ReceiverStream` dengan operasi _blocking_ (menghentikan eksekusi) dapat mempengaruhi kinerja keseluruhan aplikasi
- Tidak cocok untuk _high throughput_ karena `ReceiverStream` tidak dapat menangani _throughput_ yang tinggi dengan baik.
- Walaupun penggunaannya mudah, tentu kompleks bagi _developer_ baru yang belum terbiasa dengan `tokio`.

### Nomor 5
> In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Kita dapat melakukan pemisahan definisi protokol gRPC dan implementasi layanan gRPC menggunakan protobuf. Dengan menggunakan `proto`, kita membuat sebuah _interface_ yang dapat diimplementasikan oleh _classes_ di Rust sehingga memfasilitasi ekstensibilitas kode dengan lebih mudah. Modularitas kode juga dapat ditingkatkan dengan memisahkan kode menjadi _modules_ yang berbeda. Misalnya, kita dapat memisahkan kode _service_ dan kode _client_ ke dalam _modules_ yang berbeda. Dengan demikian, kita dapat memperbarui kode dengan lebih mudah dan memperbaiki bug dengan lebih cepat.

### Nomor 6
> In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Langkah tambahan yang bisa diimplementasi yaitu:
1. Validasi data yang diterima dari _client_ seperti jumlah pembayaran yang negatif dan lain-lain dan _error handling_-nya.
2. Implementasi logika untuk proses pembayaran seperti menghitung total harga, mengurangi stok barang, dan lain-lain.
3. Jika melibatkan pengiriman data yang berurutan, _streaming_ gRPC perlu mengirim dan menerima data secara bertahap.


### Nomor 7
> What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

gRPC memungkinkan untuk melakukan komnukasi antar layanan yang menggunakan bahasa pemrograman yang berbeda melalui protobuf. _Client_ dapat memanggil _function_ pada _server_  karena sudah ada pemahaman yang sama antara _client_ dan _server_ melalui file proto tersebut (berlaku juga sebaliknya). Hal ini mempermudah konektivitas dan operasi antar platform yang berbeda. Contohnya layanan yang ditulis dalam Rust dijalankan di _server_ dapat klien gunakan di aplikasi web dengan JavaScript dan keduanya dapat berkomunikasi dengan mulus melalui protokol gRPC tanpa harus mengimplementasikan detail protokol komunikasi.

### Nomor 8
> What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

**Kelebihan:**
1. HTTP/2 mendukung _multiplexing_ yang memungkinkan banyak permintaan dan respons untuk dikirim secara bersamaan melalui satu koneksi TCP tunggal sehingga dapat mengurangi latensi dan meningkatkan efisiensi penggunaan sumber daya.
2. HTTP/2 mendukung _streaming_ data dalam dua arah sehingga memungkinkan koneksi terus-menerus antara _client_ dan _server_. Hal ini berguna untuk transfer data yang berkelanjutan seperti streaming video.
3. HTTP/2 menggunakan _header compression_ yang mengurangi ukuran header permintaan dan respons sehingga mengurangi overhead jaringan dan mempercepat waktu muat halaman.
4. HTTP/2 mendukung penggunaan Protocol Buffers untuk serialisasi data yaitu menawarkan format data yang lebih efisien dan dapat dipahami oleh berbagai bahasa pemrograman.

**Kekurangan:**
1. Lebih kompleks untuk diimplementasi daripada HTTP/1.1 sehingga (mungkin) perlu dukungan server yang lebih canggih.
2. Belum semua _server_ mendukungnya secara penuh HTTP/2.
3. Ada beberapa perangkat atau _browser_ lama yang mungkin tidak mendukung HTTP/2 sepenuhnya.
4. HTTP/2 mungkin menjadi "_overkill_" karena menyediakan fitur yang mungkin saja tidak berguna untuk aplikasi yang sederhana.

### Nomor 9
> How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Dengan gRPC, baik _client_ maupun _server_ dapat mengirim pesan secara asinkron sehingga respons lebih cepat. Dalam kasus aplikasi _chat_, gRPC mengirim dan menerima pesan secara langsung tanpa harus menunggu _request_ atau respons tertentu. Dari sini, kita bisa bilang gRPC lebih responsif dalam situasi dimana komunikasi _real-time_ diperlukan sedangkan REST API lebih cocok untuk kasus seperti model _request_-_response_ sederhana dan _real-time communication_ tidak menjadi prioritas utama.

### Nomor 10
> What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

gRPC menerapkan skema yang didefinisikan terlebih dahulu dalam file `proto`. Jadi _client_ dan _server_ harus mengikuti format data yang telah ditetapkan dalam skema tersebut. Hal ini dilakukan agar format data konsisten dan validasi data yang lebih ketat.

REST API menggunakan JSON yang memiliki sifat lebih fleksibel dan tidak memerlukan skema yang ketat. Fleksibilitasnya lebih besar dalam mengubah struktur dan tipe data tanpa harus memperbarui skema secara eksplisit. Akan tetapi, hal ini bisa menyebabkan ketidaksesuaian (inkonsisten) format data antara _client_ dan _server_.

## Referensi
- Module 9 - High Level Networking oleh Muhammad Yusuf Khadafi dan Tim Pengajar.