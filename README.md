# Final Project
## Teknologi Komputasi Awan (B)

Kelompok 1
| NRP | Nama |
| ------ | ------ |
| 5027221001 | Dwiyasa Nakula |
| 5027221003 | Mohammad Arkananta Radithya Taratugang |
| 5027221004 | Agas Ananta Wijaya |
| 5027221009 | Asadel Naufaleo |

### Permasalahan
Anda adalah seorang lulusan Teknologi Informasi, sebagai ahli IT, salah satu kemampuan yang harus dimiliki adalah Kemampuan merancang, membangun, mengelola aplikasi berbasis komputer menggunakan layanan awan untuk memenuhi kebutuhan organisasi.(menurut kurikulum IT ITS 2023 😙)

Pada suatu saat teman anda ingin mengajak anda memulai bisnis di bidang digital marketing, anda diberikan sebuah aplikasi berbasis API File: [app.py](https://github.com/fuaddary/fp-tka/blob/main/app.py) dengan spesifikasi sebagai berikut.

Kemudian anda diminta untuk mendesain arsitektur cloud yang sesuai dengan kebutuhan aplikasi tersebut. Apabila dana maksimal yang diberikan adalah 1 juta rupiah per bulan (65 US$) konfigurasi cloud terbaik seperti apa yang bisa dibuat?

### Rancangan Arsitektur dan Tabel Harga Spesifikasi VM
Berikut adalah rancangan arsitektur yang telah kami buat untuk final project kami arsitektur
![Rancangan Arsitektur VM](images/Rancangan_VM.png)
Kami memilih untuk menggunakan Digital Ocean sebagai lingkungan cloud yang akan kami gunakan. Berikut adalah tabel harga spesifikasi VM yang kami buat
![Harga Config VM](images/Harga_VM.png)

### Langkah Implementasi dan Konfigurasi Teknologi
Buat database dan copy connection string
![Setup DB](images/setupDB.png)

Connect Database dengan MongodbCompass dan isi database tersebut
![MongoDB Compass](images/MongodbC.png)
![JSON DATABASE](images/Json_file.png)

Buat Droplet VM dan setup
![Create Worker](images/Create_worker.png)
![Install Dependency](images/install_dependency.png)

buat app.py lalu run
![run app.py](images/run_apppy.png)


### Hasil Pengujian Setiap Endpoint
GET ORDERS
![get](images/getorders.jpg)

GET ORDERS
![orderbyid](images/ordersbyid.jpg)

POST ORDERS
![post](images/post.jpg)

PUT ORDERS
![put](images/put.jpg)

DELETE ORDERS
![delete](images/delete.jpg)



### Hasil Pengujian dan Analisis Loadtesting Locust

![testing](images/testing.jpg)

### Kesimpulan dan Saran

Kesimpulan:
Final project sangat sulit

Saran:
Sebaiknya ambil jarkom dulu

