# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. Explain why it is necessary for this case, and explain why we do not use Mutex<> instead?
Kita menggunakan RwLock<> (Read-Write Lock) karena lebih efisien untuk kasus NOTIFICATIONS yang memiliki karakteristik operasi: sering dibaca, namun jarang ditulis. RwLock<> mengizinkan banyak thread untuk membaca data secara bersamaan (fungsi list_all_as_string), tetapi akan mengunci akses hanya jika ada thread yang sedang menulis data (fungsi add). Hal ini membuat performa aplikasi lebih efisien karena proses membaca tidak saling memblokir.

Jika kita menggunakan Mutex<>, aplikasi akan mengunci seluruh akses setiap kali ada thread yang berinteraksi dengan data, terlepas apakah itu hanya untuk membaca atau menulis. Ini akan menciptakan bottleneck dan menurunkan performa secara drastis jika ada banyak request yang hanya ingin melihat daftar notifikasi.

2. Compared to Java where we can mutate the content of a static variable via a static function, why did not Rust allow us to do so?
Di Java, variabel static bisa dimutasi secara bebas, namun hal ini sangat rentan menyebabkan Data Races jika diakses oleh banyak thread secara bersamaan. Rust didesain dengan keamanan memori di tingkat compiler. Compiler Rust melarang pembuatan variabel global (static) yang bisa diubah-ubah tanpa perlindungan sinkronisasi. Library lazy_static digunakan karena Rust membutuhkan waktu saat runtime untuk menginisialisasi object kompleks dan thread-safe seperti RwLock<Vec<Notification>>.

#### Reflection Subscriber-2
1. Have you explored things outside of the steps in the tutorial, for example: src/lib.rs?
Ya, saya jadi mengeksplorasi src/lib.rs. Dari file tersebut, saya mempelajari bagaimana lazy_static digunakan untuk membuat variabel global seperti REQWEST_CLIENT dan APP_CONFIG yang penting untuk mengelola resource yang digunakan bersama di seluruh aplikasi. Struct AppConfig menunjukkan bagaimana environment variable (dotenv) dan konfigurasi default digabungkan menggunakan Rocket's Figment untuk menyediakan konfigurasi yang fleksibel dan terpusat. Selain itu, fungsi compose_error_response menunjukkan cara menstandarisasi penanganan error dengan respons JSON, sehingga API menjadi lebih konsisten dan mudah digunakan.

2. How Observer pattern eases you to plug in more subscribers. How about spawning more than one instance of Main app, will it still be easy enough to add to the system?
Observer Pattern memudahkan penambahan subscriber karena publisher dan subscriber bersifat loosely coupled. Setiap instance Receiver cukup melakukan subscribe secara independen ke Main app, dan Main app akan mengirimkan notifikasi ke semua subscriber yang terdaftar tanpa perlu mengetahui detail internal mereka (cukup mengetahui URL subscriber). Sehingga, menambahkan instance Receiver baru sama sekali tidak memerlukan perubahan pada kode Main app.

Namun, menjalankan lebih dari satu instance Main app akan mempersulit sistem. Karena SUBSCRIBERS disimpan secara in-memory sebagai variabel static, setiap instance Main app akan memiliki daftar subscriber-nya sendiri yang terpisah. Subscriber yang terdaftar di satu instance tidak akan dikenali oleh instance lainnya, sehingga notifikasi bisa saja terlewat. Untuk mendukung beberapa instance Main app dengan benar, diperlukan penyimpanan seperti database atau distributed cache untuk menyinkronkan state subscriber di semua instance.

3. Have you tried to make your own Tests, or enhance documentation on your Postman collection? If you have tried those features, tell us whether it is useful for your work (it can be your tutorial work or your Group Project).
Untuk tutorial ini, saya tidak membuat test tambahan ataupun menambahkan dokumentasi Postman collection. Namun, untuk Group Project, saya sudah mulai mencoba membuat dan mendokumentasikan pada Postman collection. Hal ini sangat membantu karena setiap endpoint API menjadi terdokumentasi dengan baik, sehingga anggota tim lain dapat dengan mudah  menggunakan API. Selain itu, dengan adanya dokumentasi yang lengkap, proses pengujian menjadi lebih efisien dan menghemat waktu karena tidak perlu mengulang konfigurasi request dari awal setiap kali ingin melakukan testing.