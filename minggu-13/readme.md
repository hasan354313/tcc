## Docker - Orkestrasi menggunakan `swarm mode`

Docker memperkenalkan swarm mode pada versi 1.12. Mode ini memungkinkan pengguna untuk me-deploy container pada multiple hosts atau node, menggunakan overlay network. Swarm mode merupakan bagian dari command line interface Docker yang memudahkan pengguna untuk memanage komponen container apabila sudah familiar dengan command – command yang ada di Docker.

![docker swarm](swam/1.jpg)

### 1. Initialise Swarm Mode

![docker swarm init](swam/2.jpg)

Perintah `docker swarm init` digunakan untuk inisialisi single Docker host menjadi multiple Docker Host (swarm mode). Dengan perintah ini, Docker Engine bisa digunakan untuk clustering dan berlaku sebagai manager, dari perintah ini juga akan menghasilkan sebuah token, yang digunakan untuk menambahkan node ke cluster.

### 2. Join Cluster

![docker join cluster](swam/3.jpg)

Perintah pada gambar menunjukkan bagaimana cara mendapatkan token dengan menanyakan token kepada manager yang sudah berjalan via `swarm join-token` kemudian menyimpannya pada variable token.

![docker join cluster as worker](swam/4.jpg)

Token yang tersimpan pada variable `$token` bisa digunakan untuk mendaftarkan host yang baru sebagai worker. Secara default manager akan menerima node baru yang ditambahkan ke dalam cluster.

![docker swarm list nodes](swam/5.jpg)

`docker node ls` perintah ini akan menampilkan semua node yang ada didalam cluster.

### 3. Create Overlay Network

Pada swarm mode juga mengenalkan peningkatan pada model jaringan. Pada versi sebelumnya, docker membutuhkan penyimpanan key-value untuk memastikan konsistensi koneksi antar jaringan. Pada docker swarm mode menggunakan network overlay yang memungkinkan containers pada host yang lain untuk saling berkomunikasi, menggunakan Virtual Extensible LAN (VXLAN) yang didesain untuk cloud skala besar.

![docker overlay networks](swam/6.jpg)

Perintah diatas akan membuat overlay network bernama `skynet`. Semua container yang terhubung ke dalam jaringan ini, akan dapat saling berkomunikasi.

### 4. Deploy Service

![docker deploy with networks](swam/7.jpg)

Contoh penggunaan network untuk service http menggunakan Docker Image `katacoda/docker-http-server` dan di replica sebanyak 2 service, kemudian di buat load balance untuk kedua service yang berjalan bersamaan pada port 80. Dengan cara ini node yang menerima request bukan node yang merespon, tapi `docker load balances` melakukan requst ke semua container yang tersedia di dalam cluster.

![docker list service](swam/8.jpg)

Untuk melihat semua docker service yang berjalan, gunakan perintah `docker service ls`.

![docker ps](swam/9.jpg)

Perintah di atas untuk menampilkan container pada host.

![docker list service](swam/11.jpg)

Untuk melihat docker service mana yang merespon request uji dengan perintah `curl` seperti diatas. Terdapat container id service yang merespon request.

![docker list service](swam/12.jpg)

Untuk memastikan container ID, bisa menggunakan perintah `docker service ps http`.

### 5. Inspect State

Menggunakan service memungkinkan kita untuk memeriksa cluster dan aplikasi yang berjalan.

![docker list service](swam/13.jpg)

Dari perintah diatas terlihat respon dari service http.

![docker running tasks](swam/14.jpg)

Pada setiap node, kita bisa melihat task apa yang sedang berjalan, dengan perintah seperti gambar diatas. Dari perintah ini kita bisa mendapatkan informasi status node saat ini dan status node yang seharusnya (desired state). Jika status saat itu tidak sesuai yang diharapkan, field error akan terisi jenis errornya.

![docker ps](swam/15.jpg)

Jika tidak disertakan nama service, docker akan menampilkan semua service yang berjalan pada cluster.

![docker curl again](swam/16.jpg)

Jika kita jalankan perintah `curl` lagi, bisa kita lihat Service ID yang merespon request berbeda dengan yang pertama. Hal ini dikarenakan dengan sistem load balancing ini service akan mengirimkan request ke semua container yang berjalan pada cluster.

### 6. Scale Service

Sebuah service mengijinkan untuk scale up beberapa instance dari task yang berjalan pada cluster.

![docker service scale](swam/17.jpg)

Perintah di atas akan membuat http service berjalan sebanyak 5 containers.

Pada setiap host akan terlihat node tambahan, cek menggunakan perintah `docker ps`

![docker service scale](swam/18.jpg)

Jika kita jalankan perintah `curl` lagi akan menghasilkan host ID yang berbeda dengan yang awal kita jalankan.

![docker service scale](swam/19.jpg)