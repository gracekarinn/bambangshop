# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

#### 1. Apakah perlu menggunakan interface atau cukup satu struct?

Dalam pola desain Observer, penggunaan interface atau trait sangat penting untuk memastikan semua subscriber memiliki perilaku yang seragam. Jika di BambangShop kita hanya menggunakan satu struct `Subscriber` tanpa trait, maka akan sulit jika ingin menambahkan jenis subscriber lain dengan cara kerja yang berbeda. Dengan trait, kita dapat menentukan bahwa setiap subscriber harus memiliki metode tertentu, seperti `notify()`. Oleh karena itu, meskipun saat ini cukup dengan satu struct, penggunaan trait tetap lebih baik untuk fleksibilitas dan project yang makin kompleks.

#### 2. Apakah Vec cukup atau perlu menggunakan DashMap?
`id` dalam `Program` dan `url` dalam `Subscriber` harus bersifat unik. Jika menggunakan `Vec`, pencarian subscriber berdasarkan `url` akan menjadi lambat karena harus dicek satu per satu (O(n)). Dengan `DashMap`, pencarian lebih cepat (O(1)) karena berbasis hash map. Selain itu, `DashMap` juga mendukung akses yang aman dalam lingkungan multithreading sehingga tidak perlu takit akan konflik data. Penggunaan `DashMap` lebih efisien dan lebih cocok untuk kasus ini dibandingkan `Vec`.


#### 3. Lebih baik menggunakan DashMap atau Singleton?
Rust memiliki aturan ketat untuk memastikan program aman dalam lingkungan multithreading. Saat ini, kita menggunakan `DashMap` untuk memastikan bahwa `SUBSCRIBERS` dapat diakses oleh banyak thread tanpa masalah. Jika menggunakan Singleton, kita tetap memerlukan `Mutex` atau `RwLock` untuk mengatur akses, yang dapat membuat implementasi lebih kompleks dan memperlambat eksekusi. `DashMap` sudah menangani semua ini secara otomatis sehingga lebih sederhana dan tetap cepat. Oleh karena itu, lebih baik tetap menggunakan `DashMap` daripada Singleton.

Menurut saya, implementasi yang ada saat ini sudah cukup baik dalam menjaga fleksibilitas, efisiensi, dan keamanan akses data dalam sistem yang berjalan secara bersamaan (multithreading).

#### Reflection Publisher-2

#### 1. Mengapa perlu memisahkan Service dan Repository dari Model?
Dalam desain MVC klasik, Model mencakup data dan business logic. Namun, dengan memisahkan Service dan Repository, kita bisa menjaga kode tetap bersih dan mudah dipahami. Repository bertanggung jawab atas akses data (misalnya, mengambil dan menyimpan ke database), sedangkan Service menangani business logic. Pemisahan ini membantu menjaga Model tetap ringan dan fokus pada struktur data, serta membuat kode lebih mudah diuji dan dikelola.

#### 2. Apa yang terjadi jika hanya menggunakan Model?
Jika kita hanya menggunakan Model tanpa Service dan Repository, maka setiap Model harus menangani business logic dan akses data secara langsung. Misalnya, `Program`, `Subscriber`, dan `Notification` akan saling berinteraksi langsung dalam satu tempat yang membuat kode menjadi kompleks dan no clean code. Dengan pemisahan yang baik, perubahan dalam logika bisnis atau penyimpanan data tidak akan memengaruhi bagian lain dari aplikasi secara langsung.

#### 3. Pengalaman menggunakan Postman untuk pengujian
Postman adalah alat yang sangat membantu dalam menguji API yang sedang dikembangkan. Dengan Postman, kita bisa mengirim request HTTP dengan berbagai metode seperti GET, POST, PUT, dan DELETE untuk memastikan API bekerja dengan benar. Fiturnya antara lain adalah:
- **Collections**: Memudahkan penyimpanan dan pengelompokan request.
- **Environment Variables**: Berguna untuk menyimpan konfigurasi API agar tidak perlu diubah manual setiap saat.
- **Automated Testing**: Bisa menulis skrip untuk mengotomatiskan pengujian API.
- **Mock Servers**: Bisa mensimulasikan respons API tanpa harus menjalankan backend secara penuh.

I love postman but I prefer curl

#### Reflection Publisher-3

#### 1. Variasi Observer Pattern yang digunakan
Dalam implementasi BambangShop, kita menggunakan **Push Model**, di mana publisher secara langsung mengirim data ke semua subscriber setelah suatu peristiwa terjadi. Contohnya, ketika produk baru dibuat atau diperbarui, notifikasi langsung dikirim ke subscriber tanpa harus mereka meminta data terlebih dahulu.

#### 2. Kelebihan dan Kekurangan Pull Model
Jika kita menggunakan **Pull Model**, subscriber akan secara berkala mengecek ke publisher apakah ada perubahan. Kelebihan dari pendekatan ini adalah subscriber hanya mengambil data yang mereka butuhkan, mengurangi jumlah data yang dikirim. Namun, kekurangannya adalah dapat menyebabkan polling berlebihan dan menambah beban server, terutama jika jumlah subscriber sangat banyak.

#### 3. Dampak jika tidak menggunakan Multi-threading
Saat ini, proses notifikasi berjalan dalam thread terpisah agar pengiriman ke setiap subscriber tidak menghambat eksekusi main program. Jika kita menghilangkan multi-threading, setiap notifikasi harus dikirim satu per satu dalam urutan yang sama dengan eksekusi utama. Ini akan membuat program menjadi lambat, terutama jika banyak subscriber yang harus diberi tau. 