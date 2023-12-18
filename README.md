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
Berikut adalah rancangan arsitektur dan tabel harga spesifikasi VM yang telah kami buat untuk final project ini.
Arsitektur kami memilih untuk menggunakan Digital Ocean sebagai lingkungan cloud.

![Rancangan Arsitektur FIX](images/rancangan_fix.png)


### Langkah Implementasi dan Konfigurasi Teknologi
Buat database dan copy connection string
![Setup DB](images/setupDB.png)

Connect Database dengan MongodbCompass dan isi database tersebut
![MongoDB Compass](images/MongodbC.png)
![JSON DATABASE](images/Json_file.png)

Buat Droplet VM dan setup
![Create Worker](images/Create_worker.png)
![Install Dependency](images/install_dependency.png)

Hal yang perlu diinstall adalah
- `sudo apt update`
- `sudo apt install python3`
- `sudo apt install python3-pip -y`

- `pip install flask`
- `pip install gunicorn`
- `pip install flask_pymongo`
- `pip install gevent`

- `nano app.py`
```
from flask import Flask, jsonify, request
from flask_pymongo import PyMongo
from bson import ObjectId

app = Flask(__name__)

# Configuration for MongoDB
app.config['MONGO_URI'] = '[insert MongoDB string connection]'
mongo = PyMongo(app)

# Routes

# Get all orders
@app.route('/orders', methods=['GET'])
def get_orders():
    orders = mongo.db.orders.find()
    orders_list = []
    for order in orders:
        order['_id'] = str(order['_id'])  # Convert ObjectId to string
        orders_list.append(order)
    return jsonify({"orders": orders_list})

# Get a specific order by ID
@app.route('/orders/<string:order_id>', methods=['GET'])
def get_order(order_id):
    order = mongo.db.orders.find_one({'_id': ObjectId(order_id)})
    if order:
        order['_id'] = str(order['_id'])  # Convert ObjectId to string
        return jsonify({"order": order})
    else:
        return jsonify({"message": "Order not found"}), 404

# Create a new order
@app.route('/orders', methods=['POST'])
def create_order():
    data = request.json
    new_order = {
        'product': data['product'],
        'quantity': data['quantity'],
        'customer_name': data['customer_name'],
        'customer_address': data['customer_address']
    }
    result = mongo.db.orders.insert_one(new_order)
    new_order['_id'] = str(result.inserted_id)  # Convert ObjectId to string
    return jsonify({"message": "Order created successfully", "order": new_order})

# Update an order by ID
@app.route('/orders/<string:order_id>', methods=['PUT'])
def update_order(order_id):
    data = request.json
    updated_order = {
        'product': data.get('product'),
        'quantity': data.get('quantity'),
        'customer_name': data.get('customer_name'),
        'customer_address': data.get('customer_address')
    }
    mongo.db.orders.update_one({'_id': ObjectId(order_id)}, {'$set': updated_order})
    updated_order['_id'] = order_id
    return jsonify({"message": "Order updated successfully", "order": updated_order})

# Delete an order by ID
@app.route('/orders/<string:order_id>', methods=['DELETE'])
def delete_order(order_id):
    result = mongo.db.orders.delete_one({'_id': ObjectId(order_id)})
    if result.deleted_count > 0:
        return jsonify({"message": "Order deleted successfully"})
    else:
        return jsonify({"message": "Order not found"}), 404

if __name__ == '__main__':
    app.run(debug=True)
``` 
- `sudo apt install nginx`
- `sudo nano '/etc/nginx/sites-available/default'`
```
server {
    listen 80;
    server_name your_domain_or_ip;

    location / {
        proxy_pass http://127.0.0.1:8000;  # Assuming Gunicorn is running on port 8000
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
- `sudo nginx -t`
- `sudo service nginx restart`
- `gunicorn --bind 0.0.0.0:8000 -w 8 -k gevent app:app`
atau `gunicorn -D --bind 0.0.0.0:8000 -w 8 -k gevent app:app` untuk jalankan secara daemon

Buat Loadbalancer dengan droplet
- `sudo apt update`
- `sudo apt install nginx`
- `sudo nano '/etc/nginx/sites-available/default'`
```
upstream worker {
    server [worker1 IP];
    server [worker2 IP];
}

server {
    listen 80;

    location / {
        proxy_pass http://worker;
    }
}
```
- `sudo nginx -t`
- `sudo service nginx restart`
![loadbalancer](images/loadbalancer.jpg)

### Hasil Pengujian Setiap Endpoint
GET ORDERS
![get](images/get.jpg)

GET ORDERS
![orderbyid](images/ordersbyid.jpg)

POST ORDERS
![post](images/post.jpg)

PUT ORDERS
![put](images/put.jpg)

DELETE ORDERS
![delete](images/delete.jpg)

### Hasil Pengujian dan Analisis Loadtesting Locust
- Berapakah jumlah Request per seconds (RPS) maksimum yang dapat ditangani oleh server dengan durasi waktu load testing 60 detik? (tingkat failureure harus 0%)
![Rancangan Arsitektur FIX](images/locust4C.png)
ini hasil testing RPS yang kami dapatkan selama testing ada beberapa RPS yang lebih tinggi namun karena internet kurang stabil maka terdapat 1-4% failureure)
- Berapa jumlah peak concurrency maksimum yang dapat ditangani oleh server dengan spawn rate 25 dan durasi waktu load testing 60 detik? (tingkat failureure harus 0%)
![locust iii chart](images/locust2C.png)
![locust iii table](images/locust2T.png)
ini hasil testing dengan 1000 user dan spawn rate 25 selama 60s
- Berapa jumlah peak concurrency maksimum yang dapat ditangani oleh server dengan spawn rate 50 dan durasi waktu load testing 60 detik? (tingkat failureure harus 0%)
![locust iV chart](images/locust3C.png)
![locust iV table](images/locust3T.png)
ini hasil testing dengan 1000 user dan spawn rate 50 selama 60s
- Berapa jumlah peak concurrency maksimum yang dapat ditangani oleh server dengan spawn rate 100 dan durasi waktu load testing 60 detik? (tingkat failureure harus 0%)
![locust V chart](images/locust4C.png)
![locust V table](images/locust4T.png)
ini hasil testing dengan 1000 user dan spawn rate 100 selama 60s


### Kesimpulan dan Saran

Kesimpulan:
Pada melakukan pengujian kami memperhatikan bagaimana kinerja worker yang kami gunakan dan menariknya walaupun RPS rendah ataupun tinggi kinerja VM/Worker tidak terpengaruhi termasuk juga loadbalancer yang digunakan yang spesifikasinya lebih rendah
![kinerja VM](images/kinerjaVM.png)

- Terdapat banyak variabel yang berpengaruh dalam skenario pengujian locust, seperti:
    - Koneksi internet
    - Kuantitas dan Kualitas (spesifikasi) VM yang digunakan
    - Peak concurrency yang diinput
    - Spawn rate yang diinput
    - Durasi pengetesan
    - Saran dari kami, ketika hendak melakukan pengetesan sesuaikan dengan kebutuhan yang ingin kalian analisis dan gunakan koneksi internet yang stabil untuk memaksimalkan proses pengujian.
    - isi dari database juga mempengaruhi hasil yang mana semakin besar ukuran database semakin besar juga latencyatau response time yang bisa mencapai 2-5 detil (dimana itu lama sekali) yang mengakibatkan failure dan setiap kali testing database direset ulang.

- Ternyata ketika dilakukan pengujian, database akan semakin bertambah banyak seiring frekuensi pengujian dan hal tersebut memengaruhi jumlah RPS dan failure yang kami dapatkan. Jadi, kualitas kapasitas database juga dapat memengaruhi performa server/applikasi yang kami buat karena beban worker VM untuk melakukan GET /orders dan POST /orders juga semakin berat.

Saran:
Berdasarkan Final Project yang telah kami lakukan, kami menyarankan hal-hal sebagai berikut:
1. Pilih konfigurasi VM yang sesuai.
2. Gunakan auto-scaling untuk menyesuaikan beban kerja.
3. Terapkan kebijakan keamanan yang ketat.
4. Gunakan firewall dan enkripsi data.
5. Rutin backup data dan pastikan pemulihan efisien.
6. Aktifkan monitoring dan analisis kinerja.
7. Pilih lokasi server strategis.
8. Pantau penggunaan sumber daya.
9. Gunakan estimasi biaya dan monitoring.
10. Desain agar dapat berkembang.
11. Manfaatkan dukungan teknis penyedia cloud.

Berdasarkan itu semua, kami juga sangat menyarankan agar dapat meningkatkan spesifikasi VM menjadi lebih baik. Kita juga harus menerapkan konsep "value for money" dimana Budget juga harus ditingkatkan karena semakin besar anggaran maka fasilitas yang didapat juga semakin banyak.

Jangan pakai loadbalancer yang disediakan oleh Digital ocean, lebih mahal
