<a name="top"></a>

<!-- begin step-0 -->

## Workshop Serverless Todo App

Pada workshop ini peserta akan membuat sebuah aplikasi todo yang dibangun dengan arsitektur serverless. Backend pada aplikasi ditulis dengan Node.js dan frontend ditulis dengan HTML/Javascript/Vue.js.

Pada backend layanan yang digunakan adalah:

- AWS Lambda untuk menjalankan kode Node.js
- Amazon API Gateway untuk proxy atau routing ke API (Lambda)
- Amazon DynamoDB untuk database (NoSQL)
- Amazon SQS untuk queue
- AWS Systems Manager untuk menyimpan parameter secret
- Amazon SES untuk mengirim email
- Amazon S3 untuk menyimpan zip dari fungsi Lambda sebelum deployment

Pada frontend layanan yang digunakan adalah:

- AWS Amplify untuk hosting static website HTML/Javascript/Vue.js

Layanan lain yang digunakan adalah:

- GitHub sebagai Git repository untuk menyimpan kode yang digunakan pada workshop

![serverless-workshop-monolith](https://user-images.githubusercontent.com/469847/224513703-a4abbd98-a208-4dba-9569-a8dc41d5e688.png)

> Gambaran umum arsitektur aplikasi

<hr>
<!-- end step-0 -->

<details>
  <summary><h2>Modul 1 - Persiapan</h2></summary>
  
  <details>
    <summary><h3>Masuk ke AWS Console</h3></summary>

> **PENTING**
\
Jika Anda menggunakan akun AWS Anda sendiri maka bisa langsung menuju https://aws.amazon.com/ dan memasukkan credentials Anda untuk masuk ke AWS Console.

Ketika mengikuti workshop dengan akun AWS yang telah disiapkan, ikuti langkah-langkah berikut untuk masuk ke AWS Console.

1. Buka halaman [workshop portal](https://dashboard.eventengine.run) yang diberikan oleh penyelenggara acara. Masukkan code hash yang telah diberikan ke Anda.

![Event Engine Dashboard](https://user-images.githubusercontent.com/469847/222730689-22b6de6d-0d25-4d8c-8679-05f38c8adabb.png)

2. Pilih **Email One-time Password (OTP)**

![Email OTP](https://user-images.githubusercontent.com/469847/222732114-f0b5eb15-e118-4530-9ac5-5a4eb67a283f.png)

3. Masukkan email Anda kemudian pilih **Send passcode**. Kemudian cek email Anda untuk melihat OTP yang dikirim.

> **PENTING**: Jika email tidak ditemukan, cek juga di folder spam/junk

![Email address](https://user-images.githubusercontent.com/469847/222732276-645740a8-5211-47c0-b8d7-a697e2edfb03.png)

4. Masukkan 9 digit yang dikirim ke email Anda, kemudian pilih **Sign in**

![Passcode](https://user-images.githubusercontent.com/469847/222732419-2a04776d-5230-468a-91b1-a7d3a598f240.png)

5. Pilih **AWS Console**, kemudian **Open Console**

![AWS Console button](https://user-images.githubusercontent.com/469847/222732539-ff826a9b-dc5f-4c47-a428-4a80ef6e6726.png)

![Open Console button](https://user-images.githubusercontent.com/469847/222732664-8d98ce50-8ac1-48b5-923c-679bb6ba86a0.png)

Anda akan dibawa ke halaman dashboard AWS Console.

  </details>
  <!--Masuk ke AWS Console-->
  
  <details>
    <summary><h3>Contoh Navigasi di AWS Console</h3></summary>

Secara umum Anda dapat masuk ke halaman sebuah layanan dengan cepat adalah dengan mengetikkan nama layanan pada inputan **Search**.

![Search pada AWS Console](https://user-images.githubusercontent.com/469847/222456956-502b8cdd-1e03-4496-b41b-545daeeab8c5.png)

Kemudian Anda dapat memilih Layanan tersebut dari hasil pencarian. Anda juga dapat membukanya di-Tab browser baru agar memudahkan navigasi kedepan.

![Search AWS Lambda](https://user-images.githubusercontent.com/469847/222457768-0a012b86-d18f-448d-ad06-8df58f9182e2.png)

Jika ingin melakukan bookmark service sehingga selalu tampil di bagian atas pilih tanda **bintang**.

  </details>
  <!-- /Melakukan Navigasi di AWS Console -->

  <details>
  <summary><h3>Menggunakan Cloud IDE AWS Cloud9</h3></summary>

AWS Cloud9 adalah IDE berbasis cloud yang menyediakan fitur text editor, akses ke Terminal untuk menjalankan shell dan built-in debugger. Yang diperlukan untuk menjalankan AWS Cloud9 hanyalah web browser.

Pada workshop ini Anda akan menggunakan Cloud9 untuk menjalankan perintah di terminal dan melakukan code editing.

Untuk membuat sebuah environment di Cloud9 ikuti langkah berikut.

1. Masuk ke [AWS Cloud9](https://console.aws.amazon.com/cloud9control/home)
2. Pilih **Create environment**
![AWS Cloud9 - Create environment](https://user-images.githubusercontent.com/469847/224514038-926afe2e-c4bf-4878-9aa4-f24c7655464f.png)
3. Pada **Name** isikan &quot;workshop-{{NICKNAME}}&quot; contoh milik saya **workshop-rioastamal**
4. Pada **Environment type** pilih **New EC2 instance** pada **Instance type** pilih **t3.small**
5. Pada **Platform**, pilih **Amazon Linux 2**, **Timeout** pilih **4 hours**
6. Pada **Network Settings** bagian **Connection** pilih **AWS Systems Manager (SSM)**
7. Biarkan sisanya sesuai nilai bawaan, pilih **Create**

Tunggu beberapa saat hingga proses pembuatan environment selesai. Pilih **Open** di sebelah nama environment yang baru dibuat.

![New Cloud9 IDE Environment](https://user-images.githubusercontent.com/469847/222607823-990285f8-ca16-49e4-8a40-707fed4935d8.png)

Anda akan mendapat tampilan Cloud9. Layout default di sebelah kiri adalah file manager, tengah adalah file editor dan di bawah adalah Terminal window.

![Tampilan AWS Cloud9](https://user-images.githubusercontent.com/469847/222608860-9fcc210e-06a1-4c4d-a897-b5dbf79163c0.png)

> Anda dapat mengubah ukuran masing-masing window pane dengan menggeser pada tepian border. Silahkan tutup Welcome file.

#### Menjalankan Bootstrap Script

Sekarang jalankan perintah berikut di Terminal AWS Cloud9 untuk menginstal beberapa paket yang diperlukan selama workshop.

```sh
curl -s 'https://gist.githubusercontent.com/rioastamal/e0882594e6b34aedf03a56a6efc0b7c0/raw/12af5c42f3468b284accc8222eab70d2a539db12/bootstrap-cloud9-workshop.sh' | bash
```

Cek versi mayor dari AWS CLI harusnya 2.x.y.

```sh
aws --version
```

```
aws-cli/2.11.2 Python/3.11.2 Linux/4.14.305-227.531.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off
```

Cek versi Node harusnya 16.x.y

```sh
node --version
```

```
v16.19.1
```

  </details>
  <!-- /Menggunakan Cloud IDE AWS Cloud9 -->

  <details>
    <summary><h3>Upload Public SSH Key ke GitHub</h3></summary>

Untuk dapat melakukan push pada repository maka Anda perlu membuat SSH key di Cloud9. Public SSH key ini perlu Anda masukkan ke settings di GitHub. Jalankan perintah berikut untuk membuat SSH Key.

```
ssh-keygen
```

Biarkan kosong untuk semua pertanyaan, tekan saja ENTER.

> **PENTING**: Perintah ini akan menimpa file ~/.ssh/id_rsa. Perhatikan hal tersebut ketika menjalannya di lokal komputer atau di lingkungan yang sudah ada.

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ec2-user/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ec2-user/.ssh/id_rsa.
Your public key has been saved in /home/ec2-user/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:sEXfWVTXPucWIB57xB6n1bNnlkLgnLCcvw/KtCuZ1iw ec2-user@ip-172-31-30-222
The key's randomart image is:
+---[RSA 2048]----+
|        .. .o.o.=|
|       ...*+.O ++|
|      . .+o+X *.+|
|       +  .o = =*|
|      . S  .. .+=|
|            .   o|
|        =. o   . |
|       Eooo o    |
|      . o=.  .   |
+----[SHA256]-----+
```

Harusnya sekarang ada dua file baru `~/.ssh/id_rsa` dan `~/.ssh/id_rsa.pub` pada direktori `~/.ssh/`.

```sh
ls -l ~/.ssh
```

```
total 12
-rw------- 1 ec2-user ec2-user  991 Feb  21 01:17 authorized_keys
-rw------- 1 ec2-user ec2-user 1679 Feb  21 02:40 id_rsa
-rw-r--r-- 1 ec2-user ec2-user  407 Feb  21 02:40 id_rsa.pub
```

#### Salin isi ~/.ssh/id_rsa.pub

```sh
cat ~/.ssh/id_rsa.pub
```

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCqeyLR64hC6OV1RLldH25q07ZribVthVjgcP0Pa8DXS5bcQDMhbNsTLZyfCsLZJZ8ajzNM2lIEndFGMsrLElWNjMMEnyfOV7AMZ/cyq7ult90RjnYgLUtn6Ju1FFQBCIEm6qlxoYjHR7NVkKqC6akAKe6PVwLsJ2XrmCy+ta1oZVY65pfLqvfg5Q0vFE84kmEldalfNm2aGTWwZeEdJ9ngL+dHPdzW2WRk7GaPBbgzBsqepyplAm5fjPPopWyE1W8Eqzb8iSaTcApBKZLdccZCvRb1bEQFcnL5ToSZzhyyXuZfsVn9U6FpDkqBtzPf9GWXfuXrLzgyuNS1jNJBT+3H ec2-user@ip-172-31-26-248.ap-southeast-1.compute.internal
```

> **CATATAN**: Isi dari file ~/.ssh/id_rsa.pub Anda pasti berbeda dengan milik saya.

Kita akan memasukkan public key tersebut ke akun GitHub.

1. Buka akun GitHub Anda masuk ke **Settings**

![GitHub Settings](https://user-images.githubusercontent.com/469847/222455669-2a5234b8-3680-42d3-8df5-d5e266237c43.png)

2. Pilih **SSH and GPG keys**, pilih **New SSH key**
3. Pada title isikan &quot;awsug-workshop-cloud9&quot;
4. Pada **Key type** pilih **Authentication Key**
5. Paste isi dari `~/.ssh/id_rsa.pub` ke inputan **Key**
6. Pilih **Add SSH key**

Setelah proses selesai harusnya Anda dapat melakukan push ke repository pada akun GitHub Anda.

#### Konfigurasi Nama dan Email untuk Commit

Konfigurasi ini berguna untuk memberi commit nama dan email yang sesuai dengan akun GitHub Anda.

> **PENTING**: Ganti {{NAME}} {{EMAIL}} dengan email yang Anda gunakan pada GitHub.

```
git config --global user.name "{{NAME}}"
git config --global user.email "{{EMAIL}}"
```

```sh
git config -l
```

```
credential.helper=!aws codecommit credential-helper $@
credential.usehttppath=true
core.editor=nano
user.name=Rio Astamal
user.email={{EMAIL_ANDA}}
```

  </details>
  <!-- /Upload Public SSH Key ke GitHub -->

</details>
<hr>

<details>
  <summary><h2>Modul 2 - Membuat Layanan Dasar</h2></summary>
  
  <details>
    <summary><h3>Membuat S3 Bucket</h3></summary>

Bucket ini akan digunakan untuk menyimpan kode fungsi Lambda yang kemudian akan dideploy lewat halaman console AWS Lambda.

1. Masuk pada halaman [Amazon S3](https://s3.console.aws.amazon.com/s3/home). Anda dapat melakukannya lewat inputan _Search_ disisi atas AWS console lalu ketik &quot;S3&quot; - pilih **S3** - pilih **Create bucket**
![Create S3 Bucket](https://user-images.githubusercontent.com/469847/224529647-bb97b18d-ae15-48c2-887a-134c2edd6d87.png)
2. Pada **Bucket name** isikan &quot;serverless-workshop-{{YYYYMM}}-{{NICKNAME}}&quot;. 
    - Ganti {{YYYYMM}} dengan tahun bulan, misal untuk Maret 2023 gunakan **202303**.
    - Ganti {{NICKNAME}} dengan nama anda atau sesuatu yang unik. Hanya inputkan alphanuric saja, contoh jika nama saya Rio Astamal maka gunakan **rioastamal**.
    - Contoh lengkap untuk nama S3 Bucket **serverless-workshop-202303-rioastamal**
3. Pada **AWS Region** pilih _Asia Pacific (Singapore) ap-southeast-1_
4. Biarkan opsi lainnya dengan nilai bawaan, kemudian pilih tombol **Create bucket**

Harusnya sekarang Anda memiliki bucket baru, contoh milik saya **serverless-workshop-202303-rioastamal**.

![S3 Bucket baru](https://user-images.githubusercontent.com/469847/222464525-2cec1ae9-f98d-40af-8dcd-c25b7cd63525.png)

  </details>
  <!-- Membuat S3 Bucket -->

  <details>
    <summary><h3>Membuat DynamoDB Table</h3></summary>

Kita akan menggunakan Amazon DynamoDB untuk menyimpan data user dan Todo list. Untuk itu Anda perlu membuat sebuah DynamoDB Table baru.

Disini kita hanya menggunakan satu tabel saja dan menerapkan konsep [Single Table Design](https://aws.amazon.com/blogs/compute/creating-a-single-table-design-with-amazon-dynamodb/) pada DynamoDB.

1. Masuk pada halaman Amazon DynamoDB. Pada **Search** ketik &quot;dynamodb&quot;, pilih **DynamoDB**, pilih **Create table**
![Create DynamoDB table](https://user-images.githubusercontent.com/469847/224529867-c0ef7861-66d7-46a6-b7b2-b53bfb60e400.png)
2. Pada **Table name** isikan &quot;serverless-todo-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-rioastamal**
3. Pada **Partition key** isikan &quot;pk&quot; dengan tipe **String**
4. Pada **Sort key** isikan &quot;sk&quot; dengan tipe **String**
4. Biarkan opsi lainnya sesuai nilai bawaan, kemudian pilih tombol **Create table**

Tunggu beberapa saat maka tabel akan siap. Itu ditandai dengan status dari tabel yaitu **Active**.

![DynamoDB table](https://user-images.githubusercontent.com/469847/222465947-8233779f-3f5c-421c-a3ba-1bcd58793f1a.png)

#### Contoh data

Pada aplikasi yang dibuat semua item akan diidentifikasi dari kombinasi `pk` dan `sk`. Format dari data untuk user adalah:

pk | sk
---|---
user#{{USERNAME}} | user

Format data untuk todo adalah:

pk | sk
---|---
todo#{{TODO_ID}} | todo#{{USERNAME}}

Berikut contoh item-item pada tabel.

pk | sk | data | created_at
---|----|------|-----------
user#rioastamal | user | `{ "username": "rioastamal", "password": "...", "fullname": "Rio Astamal", ... }` | 2023-02-22T00:32:43.255Z
todo#workshop123 | todo#rioastamal | `[{ "id": "todo-1", "title": "Create serverless workshop", "completed": false }]` | 2023-02-22T00:34:43.255Z

Pada DynamoDB jumlah kolom dan tipe data dari setiap item tidak harus sama, kecuali kolom `pk` dan `sk` yang harus selalu ada karena merupakan partition key dan sort key.
  </details>
  <!-- /Membuat DynamoDB Table -->

  <details>
    <summary><h3>Membuat Identity di Amazon SES</h3></summary>

Untuk dapat mengirim email di Amazon Simple Email Service (Amazon SES) maka diperlukan identity. Identity ini digunakan ketika proses pengiriman email. Bisa berupa verifikasi domain, subdomain atau email.

Ketika akun masih berada pada Sandbox maka alamat penerima juga perlu kita masukkan ke verified identity.

Pada langkah ini kita akan membuat dua verified identity email, satu untuk pengirim dan satu untuk penerima. Kita akan memanfaatkan tanda plus **+** pada alamat email untuk membuat alias.

#### Membuat Identity untuk Pengirim

1. Masuk pada halaman [Amazon SES](https://console.aws.amazon.com/ses/home#/homepage), pilih **Create identity** (atau Pilih menu **Configuration** di samping kiri, pilih **Verified identities**, pilih **Create identity**)
![Create SES Identity](https://user-images.githubusercontent.com/469847/224530183-fc02c8d0-4027-4929-bae7-9914414a5235.png)
2. Pada **Identity type** pilih **Email address**
3. Pada **Email address** isikan &quot;{{EMAIL_ANDA}}+sender@example.com&quot;, contoh adalah **john+sender@gmail.com**
4. Pilih **Create identity**

Anda akan menerima email verifikasi dari Amazon SES. Klik link verifikasi tersebut untuk memvalidasi identity dari email pengirim.

#### Membuat Identity untuk Penerima

1. Masuk pada halaman [Amazon SES](https://console.aws.amazon.com/ses/home#/homepage), pilih **Create identity** (atau Pilih menu **Configuration** di samping kiri, pilih **Verified identities**, pilih **Create identity**)
2. Pada **Identity type** pilih **Email address**
3. Pada **Email address** isikan &quot;{{EMAIL_ANDA}}+receiver@example.com&quot;, contoh adalah **john+receiver@gmail.com**
4. Biarkan isian lainnya sesuai bawaan, pilih **Create identity**

Cek email Anda untuk link verifikasi. Setelah proses verifikasi selesai harusnya Anda memiliki dua verified identity dari satu alamat email.

![Amazon SES verified identity](https://user-images.githubusercontent.com/469847/224512646-dd4c033c-8fd9-42d0-99f2-d26d3251b21d.png)
  
  </details>
  <!-- /Membuat Identity di Amazon SES -->

  <details>
    <summary><h3>Membuat Parameter di AWS Systems Manager</h3></summary>

API menggunakan JWT untuk proses otentikasi. Dalam proses pembuatan JWT token diperlukan nilai _secret_ untuk proses enkripsi. _Secret_ ini bisa saja diletakkan di environment variable namun cara yang lebih aman adalah menyimpan dan mengenkripsi nilainya ditempat terpisah.

Untuk itu digunakan AWS Systems Manager Parameter Store.

1. Masuk pada halaman [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home)
2. Pada menu **Application Management** pilih **Parameter Store** kemudian **Create parameter**
![Menu Parameter Store](https://user-images.githubusercontent.com/469847/224530635-5a4ee600-4374-4d93-9383-99b6ea9334dc.png)
3. Pada **Name** isikan &quot;/{{NICKNAME}}/serverless-todo/development/jwt-secret&quot; contoh milik saya **/rioastamal/serverless-todo/development/jwt-secret**
4. Pada **Tier** pilih **Standard**
5. Pada **type** pilih **SecureString**, biarkan opsi lain sesuai bawaan, pada **Value** isikan &quot;_workshop-serverless-todo-123456_&quot;
6. Pilih **Create parameter**

> **PENTING**: Pada production nilai dari jwt-secret ini harusnya karakter yang random. Contoh dapat menggunakan OpenSSL seperti `openssl rand -base64 48`.

Kita akan mengambil dan menggunakan nilai parameter pada code Node.js yang diperlukan oleh API.

![Parameter Store Details](https://user-images.githubusercontent.com/469847/222470132-67498505-f17d-4a1a-9dba-5a6a278584f2.png)

  </details>
  <!-- /Membuat Parameter di AWS Systems Manager -->

</details>
<hr>

<details>
  <summary><h2>Modul 3 - Membuat Backend</h2></summary>
  
  <details>
    <summary><h3>Membuat Fungsi Lambda</h3></summary>

Kita akan membuat sebuah fungsi pada AWS Lambda untuk menjalankan aplikasi yang ditulis dengan Node.js. Runtime Node.js adalah salah satu official runtime yang didukung oleh Lambda.

Fungsi ini akan kita integrasikan dengan Amazon API Gateway sebagai proxy/gateway agar bisa diakses dari internet.

1. Pada inputan _Search_ di AWS console ketik &quot;lambda&quot; pilih **Lambda**, pilih **Create a function**
![Create a Lambda function](https://user-images.githubusercontent.com/469847/224604692-ceb202c9-e360-4ea8-b843-a54c58f58d4d.png)
2. Pilih **Author from scratch**
3. Pada **Function name** isikan &quot;serverless-todo-api-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-api-rioastamal**
4. Pada **Runtime** pilih **Node.js 16.x** kemudian **Architecture** pilih **x86_64**
5. Sisanya biarkan sesuai nilai bawaan, kemudian pilih **Create function**

Sekarang sebuah fungsi Lambda telah dibuat. Kita akan mencoba menjalankan fungsi tersebut.

![Fungsi Lambda](https://user-images.githubusercontent.com/469847/222830710-331677c7-838f-4860-b994-5c2016d8f7e9.png)

#### Membuat Test event

Sebuah fungsi Lambda dieksekusi ketika ada sebuah trigger event tertentu. Kita akan mensimulasikan trigger dari sebuah event yang dikirim oleh API Gateway.

1. Pada halaman detil fungsi Lambda yang baru dibuat, pilih tab **Test** kemudian akan tampil konfigurasi **Test event**.
![Tab Test](https://user-images.githubusercontent.com/469847/222831701-07364e7d-e79f-4c3a-8f34-9f7dcc58613e.png)
2. Pada **Test event action** pilih **Create new event**
3. Pada **Event name** isikan **api-gateway-proxy**
4. Pada **Event sharing settings** pilih **Private**
5. Pada **Template** pilih **API Gateway AWS Proxy**
6. pilih tombol **Save** lalu **Test**

Pada bagian **Execution result** akan muncul output berupa JSON string dari code Node.js yang dijalankan oleh Lambda.

![Execution result](https://user-images.githubusercontent.com/469847/222488291-0f8a6275-2d02-40a2-80c6-47e66fd66f40.png)

#### Mengubah Code Javascript

Kembali ke tab **Code** dan edit code Javascript `index.js` menjadi seperti berikut.

```javascript
// exports.hanlder = async (event) => {
exports.main = async (event) => {
    // TODO implement
    const response = {
        statusCode: 200,
        // body: 'Hello from Lambda!',
        body: JSON.stringify(event, null, 2),
    };
    return response;
};
```

Simpan (**File** - **Save**) code tersebut, kemudian pilih **Deploy**, lalu pilih **Test**. Harusnya respon yang didapat adalah sebuah error.

```json
{
  "errorType": "Runtime.HandlerNotFound",
  "errorMessage": "index.handler is undefined or not exported",
  "trace": [
    "Runtime.HandlerNotFound: index.handler is undefined or not exported",
    "    at Object.UserFunction.js.module.exports.load (file:///var/runtime/index.mjs:1038:15)",
    "    at async start (file:///var/runtime/index.mjs:1200:23)",
    "    at async file:///var/runtime/index.mjs:1206:1"
  ]
}
```

Hal itu karena handler fungsi Lambda tersebut dikonfigurasi dengan nilai `index.handler`. Artinya AWS Lambda akan menjalankan method `handler` yang di-export oleh file `index.js`. Kita telah mengganti method-nya dari `exports.handler` ke `exports.main` sehingga error tersebut terjadi.

Untuk mengatasinya ubah konfigurasi handler dari fungsi Lambda ini.

![Runtime Settings](https://user-images.githubusercontent.com/469847/224609215-55383930-89be-40a9-8e7c-193fb8872f0a.png)

1. Pada tab **Code** scroll ke bagian **Runtime settings** lalu pilih **Edit**
2. Pada bagian **Handler** ganti nilai dari `index.handler` menjadi `index.main`
3. Pilih tombol **Save** lalu kembali pilih tombol **Test**

Harusnya sekarang fungsi berjalan normal dan mengembalikan output sesusai dengan isi dari test event **api-gateway-proxy** pada atribut `body`.

![Execution test result API GW](https://user-images.githubusercontent.com/469847/222489811-e3f9b9d5-da1a-48e9-9f10-e7b65b2eb70c.png)

Dari sini Anda diharapkan memahami korelasi antara konfigurasi Handler di Lambda dan bagaimana harusnya penamaan file dan nama method yang di-export.

  </details>
  <!-- /Membuat Fungsi Lambda -->

  <details>
    <summary><h3>Menghubungkan Fungsi Lambda ke Amazon API Gateway</h3></summary>

Amazon API Gateway akan bertindak sebagai router yang mengarahkan request ke fungsi Lambda yang dibuat.

1. Masuk pada halaman Amazon API Gateway. Pada inputan _Search_ AWS console lalu ketik &quot;api gateway&quot; - pilih **API Gateway** 
2. Pada **Choose an API type** pilih **HTTP API** kemudian pilih **Build**
![Create a HTTP API](https://user-images.githubusercontent.com/469847/224609729-610c23cd-644b-4745-a890-48ff907feb95.png)

Konfigurasi berikut akan menghubungkan fungsi Lambda yang dibuat dengan sebuah HTTP API.

1. Pada **Integrations** pilih **Add Integration**, pilih **Lambda**, **AWS Region** pilih **ap-southeast-1**, **Lambda function** pilih fungsi Lambda yang telah dibuat, **Version** pilih 2.0
2. Pada **API name** isikan &quot;serverless-todo-gw-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-gw-rioastamal**, pilih **Next**
![Create API Gateway Step 1](https://user-images.githubusercontent.com/469847/224610349-940312cf-afdf-4ebc-86f6-5d75cc7e05be.png)
3. Kemudian pada bagian routing **Method** pilih **ANY**, **Resource Path** ganti isinya dengan **/{proxy+}**, **Integration target** pilih fungsi Lambda Anda, pada kasus saya adalah **serverless-todo-api-rioastamal**
4. Pilih **Next**
![Create API Gateway Step 2](https://user-images.githubusercontent.com/469847/224611110-e17059b2-ca7e-486c-8658-a0843bde0be0.png)
5. Pada konfigurasi stage, pada **Stage name** pilih **$default** dan pastikan **Auto-deploy** aktif
6. Pada halaman review jika sudah sesuai, pilih **Create**
![Create API Gateway Review Step](https://user-images.githubusercontent.com/469847/224612157-2585f6a0-b846-4ada-a413-21143da2330d.png)

Anda akan dibawa pada halaman detil dari HTTP API. Lihat pada bagian Stage  terdapat **Invoke URL** yang merupakan alamat dari HTTP API. 

![Invoke URL](https://user-images.githubusercontent.com/469847/222491008-ac4481ab-1604-4af8-a472-6c13d8d8416e.png)

Buka link tersebut untuk mengeksekusi fungsi Lambda yang baru dibuat. Outputnya adalah JSON string yang isinya adalah request yang dikirimkan oleh Amazon API Gateway. Milik saya outputnya seperti berikut.

```json
{
  "version": "2.0",
  "routeKey": "ANY /{proxy+}",
  "rawPath": "/",
  "rawQueryString": "",
  "headers": {
    "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8",
    "accept-encoding": "gzip, deflate, br",
    "accept-language": "en-US,en;q=0.5",
    "content-length": "0",
    "host": "syvyjs8mej.execute-api.ap-southeast-1.amazonaws.com",
    "sec-fetch-dest": "document",
    "sec-fetch-mode": "navigate",
    "sec-fetch-site": "cross-site",
    "sec-fetch-user": "?1",
    "upgrade-insecure-requests": "1",
    "user-agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:102.0) Gecko/20100101 Firefox/102.0",
    "x-amzn-trace-id": "Root=1-63fdf4e7-1ea77c546d6226ed664d77a8",
    "x-forwarded-for": "180.253.89.124",
    "x-forwarded-port": "443",
    "x-forwarded-proto": "https"
  },
  "requestContext": {
    "accountId": "079418010844",
    "apiId": "syvyjs8mej",
    "domainName": "syvyjs8mej.execute-api.ap-southeast-1.amazonaws.com",
    "domainPrefix": "syvyjs8mej",
    "http": {
      "method": "GET",
      "path": "/",
      "protocol": "HTTP/1.1",
      "sourceIp": "180.253.89.124",
      "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:102.0) Gecko/20100101 Firefox/102.0"
    },
    "requestId": "BDM0Rg8AyQ0EMXA=",
    "routeKey": "ANY /{proxy+}",
    "stage": "$default",
    "time": "28/Feb/2023:12:34:47 +0000",
    "timeEpoch": 1677587687813
  },
  "pathParameters": {
    "proxy": ""
  },
  "isBase64Encoded": false
}
```

Aplikasi ini masih bersifat monolith jadi route path `/{proxy+}` berfungsi sebagai catch-all route sehingga path apapun akan ditangkap dan diteruskan ke target yang ditentukan dalam hal ini fungsi Lambda Anda.

  </details>
  <!--Menghubungkan Fungsi Lambda ke Amazon API Gateway-->

  <details>
    <summary><h3>Menjalankan Todo API di AWS Cloud9</h3></summary>

Menjalankan Todo API di AWS Cloud9 sama halnya kita menjalankannya di mesin lokal. Kita akan melakukan clone project todo api yang telah disiapkan.

1. Jika Cloud9 Anda belum terbuka, maka masuk pada halaman [AWS Cloud9](https://console.aws.amazon.com/cloud9/home), Pilih **Open** untuk membuka environment yang telah ada sebelumnya
2. Buka terminal baru jika belum ada, pastikan berada pada `/home/ec2-user/environment`

```sh
cd ~/environment
```

3. Clone project Serverless Todo API

```sh
git clone https://github.com/rioastamal-examples/serverless-todo-express-api.git
```

4. Masuk pada direktori project dan install dependencies menggunakan `npm`

```sh
cd serverless-todo-express-api
```

```sh
npm install --omit=dev
```

Untuk menjalankan API kita butuh menyuplai beberapa environment variable yaitu:
- `APP_TABLE_NAME`: DynamoDB table
- `APP_PARAMSTORE_JWT_SECRET_NAME`: nama Parameter Store untuk jwt-secret
- `APP_FROM_EMAIL_ADDR`: alamat email pengirim yang sudah diverifikasi di Amazon SES

Kembali pada terminal di AWS Cloud9. Jalankan perintah ini untuk menset environment variable di Terminal.

> **PENTING**: Sesuaikan nilai dari setiap environment variable ini dengan milik Anda. Anda dapat menyalin kode dibawah ini ke file baru (tanpa menyimpan) di Cloud9 lakukan perubahan baru paste ke Terminal.

```sh
export APP_TABLE_NAME=serverless-todo-{{NICKNAME}}
export APP_PARAMSTORE_JWT_SECRET_NAME=/{{NICKNAME}}/serverless-todo/development/jwt-secret
export APP_FROM_EMAIL_ADDR=EMAIL.SAYA+sender@gmail.com
```

Kemudian jalankan file `local.js`. 

```sh
node local.js
```

```
API server running on port 8080
```

> **PENTING**: Selama mencoba API yang dijalankan lewat `node local.js` jika Anda menemui error "_ExpiredTokenException: The security token included in the request is expired_" maka kembali ke tab terminal yang menjalankan `node local.js` tekan CTRL+C untuk menghentikan server dan jalankan ulang `node local.js`. Harusnya sekarang call API sudah tidak mengembalikan error tersebut.

Buka terminal session baru pada AWS Cloud9 dengan memilih tanda plus **+** kemudian pilih **New Terminal**. Pada terminal baru tersebut jalankan perintah berikut untuk mengetes respon API di endpoint `/protected`.

![Cloud9 open new terminal](https://user-images.githubusercontent.com/469847/224682926-a01b3508-85bd-4be2-88d1-c97026eb2581.png)

```sh
curl -s -D /dev/stderr http://localhost:8080/protected | jq .
```

```
HTTP/1.1 401 Unauthorized
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 26
ETag: W/"1a-pljHtlo127JYJR4E/RYOPb6ucbw"
Date: Tue, 28 Feb 2023 14:03:40 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "Unauthorized"
}
```

Seharusnya API mengembalikan HTTP status 401 yang artinya dibutuhkan otentikasi untuk mengakses endpoint tersebut.

Isi dari file `local.js` mirip seperti kebanyakan script untuk menjalankan aplikasi Node.js.

```javascript
const app = require('./src/index.js');

const port = process.env.APP_PORT || 8080;

app.listen(port, function() {
  console.log(`API server running on port ${port}`);
});
```

Dimana aplikasi akan melakukan bind pada port default `8080`. Object `app` diimpor dari file utama yaitu `src/index.js`. File ini tidak digunakan ketika aplikasi dijalankan di AWS Lambda karena format request/response yang berbeda dengan HTTP request normal.

#### Mencoba Endpoint POST /register

Selanjutnya mari kita coba melakukan registrasi pengguna. Endpoint yang digunakan adalah `/register`. Pastikan email yang digunakan adalah yang sudah didaftarkan di verified identity karena status Amazon SES masih dalam sandbox.

> **PENTING**: Ganti [EMAIL_PENERIMA] dengan email receiver yang diverifikasi di Amazon SES. Anda dapat menyalin kode dibawah ini ke file baru (tanpa menyimpan) di Cloud9 lakukan perubahan baru paste ke Terminal.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
http://localhost:8080/register -d '
{
  "username": "workshop-user1",
  "password": "workshop123",
  "fullname": "User One",
  "email": "[EMAIL_PENERIMA]"
}' | jq .
```

```
HTTP/1.1 201 Created
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 42
ETag: W/"2a-nMoFx54+czTntmSLXl3mqIsZV4A"
Date: Tue, 21 Feb 2023 15:45:00 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "User registered successfully"
}
```

Cek email untuk memastikan API telah mengirim welcome email. Provider email mungkin mengklasifikasikan email sebagai spam karena absennya beberapa atribut seperti SPF dan DKIM. 

Hal ini tidak masalah karena kita hanya melakukan tes. Jadi pastikan untuk cek juga di folder spam/junk.

![Welcome email inbox](https://user-images.githubusercontent.com/469847/224705316-3c4d0fc3-df5d-4311-925d-baf2d964706d.png)

#### Mencoba Endpoint POST /login

Sekarang coba login untuk mendapatkan JWT token.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
http://localhost:8080/login -d '
{
  "username": "workshop-user1",
  "password": "workshop123"
}' | jq .
```

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 232
ETag: W/"e8-MT+u0ta7SmxYtf5v5jjibWl/UnY"
Date: Tue, 21 Feb 2023 16:25:55 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "token": "SOME_LONG_JWT_TOKEN"
}
```

#### Mencoba Endpoint PUT /todos/:id

Gunakan token yang didapat sebelumnya pada header `Authorization`. Jalankan perintah untuk membuat environment variable baru `JWT_TOKEN`.

> **PENTING**: Ganti nilai SOME_LONG_JWT_TOKEN dengan nilai token yang anda dapat dari respon /login

```sh
export JWT_TOKEN="SOME_LONG_JWT_TOKEN"
```

Sekarang buat sebuah todo list sederhana dengan ID &quot;{{NICKNAME}}-1&quot;, dalam contoh saya menggunakan **rioastamal-1**.

> **PENTING**: Anda dapat menyalin dulu perintahnya ke editor di Cloud9 lalu merubah {{NICKNAME}}-1 menjadi nickname anda sendiri baru paste di Terminal.

```sh
curl -s -D /dev/stderr -XPUT \
-H "Content-type: application/json" \
-H "Authorization: Bearer $JWT_TOKEN" \
http://localhost:8080/todos/{{NICKNAME}}-1 -d '
[
  {
    "id": "todo-1",
    "title": "Workshop Serverless",
    "completed": false
  },
  {
    "id": "todo-2",
    "title": "Pulang makan",
    "completed": false
  }
]' | jq .
```

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 37
ETag: W/"25-XPFgY3+pqPIQgFjmpJbmM77Ikbo"
Date: Tue, 21 Feb 2023 16:40:44 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{
  "message": "Todo successfully added"
}
```

#### Mencoba Endpoint GET /todos/:id

Sekarang coba untuk dapatkan Todo item yang baru saja dibuat. Sesuaikan dengan todo ID dan token Anda sendiri.

> **PENTING**: Ganti {{NICKNAME}}-1 dengan milik Anda sendiri yang dimasukkan dari perintah sebelumnya.

```sh
curl -s -D /dev/stderr \
-H "Content-type: application/json" \
-H "Authorization: Bearer $JWT_TOKEN" \
http://localhost:8080/todos/{{NICKNAME}}-1 | jq .
```

```
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 122
ETag: W/"7a-N/KgtPWXVbcHR0srdVVCfOmcjc0"
Date: Tue, 28 Feb 2023 16:42:40 GMT
Connection: keep-alive
Keep-Alive: timeout=5

[
  {
    "title": "Workshop Serverless",
    "id": "todo-1",
    "completed": false
  },
  {
    "title": "Pulang makan",
    "id": "todo-2",
    "completed": false
  }
]
```

  </details>
  <!--Menjalankan Todo API di AWS Cloud9-->
  
  <details>
    <summary><h3>Melihat Data di DynamoDB</h3></summary>

Kita telah melakukan beberapa operasi penulisan data yaitu ketika memanggil endpoint `POST /register` dan `PUT /todos/:id`. Mungkin Anda ingin melihat bagaimana data tersebut disimpan pada tabel di DynamoDB.

1. Masuk ke halaman console DynamoDB.
2. Pilih **Explore Items** dari menu di samping, pada **Tables** pilih tabel yang Anda buat, contoh milih saya **serverless-todo-rioastamal**
3. Pada **Scan or query items**, pilih **Scan**
4. Pilih **Run** untuk menampilkan semua item di tabel tersebut
![Scan Items DynamoDB](https://user-images.githubusercontent.com/469847/224723567-43cbd31e-ec7c-404c-a872-e475f14a25d2.png)

Hasil akan ditampilkan pada bagian **Items returned**. Pada contoh harusnya terdapat dua item yaitu sebuah data user dan sebuah todo. Pilih item todo yang dibuat untuk melihat detil dari item tersebut.

![Item on DynamoDB](https://user-images.githubusercontent.com/469847/224724397-5548394b-555c-4fc8-a628-e7f1dda2c916.png)

Anda akan dibawa ke halaman detil dari item yang berupa sebuah JSON. Anda dapat melakukan edit item tersebut jika mau. Namun pada contoh ini pilih **Cancel** untuk menutup kembali halaman detil item.

> **PENTING**: Pada production perintah **Scan** tidak direkomendasikan karena akan membaca seluruh tabel. Hal itu tidak masalah jika jumlah datanya kecil, namun jika jumlah data besar akan berpengaruh pada pemrosesan dan biaya. Selalu gunakan **[Query](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)** jika dimungkinkan. 
  </details>

  <details>
    <summary><h3>Deploy Code ke AWS Lambda</h3></summary>

Terdapat dua cara utama untuk mengupload code ke AWS Lambda. Pertama adalah langsung dari komputer lokal Anda atau dari S3 Bucket. Kita akan menggunakan cara yang disebut kedua. File yang diupload dalam format zip.

Handler yang akan digunakan oleh fungsi Lambda ini adalah method `main` pada file `lambda.js`.

Pastikan Anda berada pada root directory dari project serverless-todo-express-api. Kita akan memaket code API yang ada dalam sebuah zip.

Pastikan Anda berada di direktori project serverless-todo-api jika belum.

```sh
cd ~/environment/serverless-todo-express-api/
```

Jalankan perintah berikut untuk mengupload code ke S3 Bucket. Nama bucket saya adalah **serverless-workshop-202303-rioastamal**, sesuaikan milik Anda sendiri.

> PENTING: Ganti [NAMA_BUCKET] dengan S3 bucket yang Anda buat diawal

```sh
export APP_FUNCTION_BUCKET=[NAMA_BUCKET]
```

Lalu jalankan file `build.sh`

```sh
bash build.sh
```

```
Downloading app dependencies...

added 156 packages, and audited 157 packages in 3s

8 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
Creating zip...
Uploading zip to S3 Bucket...
upload: .build/tmp/serverless-todo-api.zip to s3://serverless-workshop-202303-rioastamal/serverless-todo-api.zip
```

> CATATAN: File dan direktori yang dipaket dalam zip adalah <br>- `src/`<br>- `node_modules/`<br>- `lambda.js`

Setelah selesai seharusnya terdapat sebuah file zip dengan nama **serverless-todo-api.zip** pada bucket.

1. Masuk pada halaman [Amazon S3](https://s3.console.aws.amazon.com/s3/get-started)
2. Pilih **Buckets** dari menu, pilih bucket yang telah dibuat.
3. Pilih file **serverless-todo-api.zip**
4. Pada tab **Properties** copy nilai yang ada pada **Object URL**

![Copy Object URL](https://user-images.githubusercontent.com/469847/222493102-1eec90a9-a6db-4ed8-9ec7-4f72883a9e49.png)

Berikutnya kita akan mengupload zip dari bucket tersebut ke fungsi Lambda.

![Upload from](https://user-images.githubusercontent.com/469847/222837918-39f71233-9902-4239-b9a5-b7173fc7d8e8.png)

1. Masuk ke console [AWS Lambda](https://console.aws.amazon.com/lambda/home)
2. Pilih **Functions** pilih fungsi yang telah dibuat
3. Pada tab **Code**, pilih **Upload from**, pilih **Amazon S3 location**
4. Pada **Amazon S3 link URL** isikan dari nilai dari Object URL yang dicopy sebelumnya

Berikutnya memasukkan nilai ke environment variable.

1. Masuk halaman fungsi Lambda yang telah dibuat
2. Pilih tab **Configuration**, pilih **Environment variables**, pilih **Edit**
3. Pilih **Add environment variable**, masukkan nilai sesuai milik Anda:
   - Key: `APP_TABLE_NAME`, Value: `serverless-todo-{{NICKNAME}}`
   - Key: `APP_PARAMSTORE_JWT_SECRET_NAME`, Value: `/{{NICKNAME}}/serverless-todo/development/jwt-secret`
   - Key: `APP_FROM_EMAIL_ADDR`, Value: `EMAIL.ANDA+sender@gmail.com`
4. Pilih **Save**

![Environment variable](https://user-images.githubusercontent.com/469847/224726513-6ca251d2-dd19-476e-bd68-4886a398f193.png)

#### Mencoba API lewat API Gateway

Kita akan mencoba apakah API berjalan normal ketika dijalankan di AWS Lambda. 

1. Masuk pada halaman [API Gateway](https://console.aws.amazon.com/apigateway/home)
2. Pilih API yang telah dibuat, contoh milih saya **serverless-todo-gw-rioastamal**
3. Copy nilai URL yang ada pada **Invoke URL**

> **PENTING**: Jangan lupa untuk selalu mengganti URL yang ditunjukkan dalam diperintah dengan URL API Gateway Anda sendiri

Kembali pada terminal di AWS Cloud9. Jalankan perintah berikut untuk mencoba API. Ganti URL `{{INVOKE_URL}}` dengan milik Anda sendiri.

```sh
export INVOKE_URL={{INVOKE_URL}}
```

```sh
curl -s -D /dev/stderr $INVOKE_URL/protected | jq .
```

```
HTTP/2 500 
date: Tue, 21 Feb 2023 04:13:29 GMT
content-type: application/json
content-length: 35
apigw-requestid: BFWUKhzKSQ0EPVw=

{
  "message": "Internal Server Error"
}
```

Oops, kenapa ya? Masih ingat korelasi antara Handler pada Runtime settings di Lambda dan nama file Javascript?

Terakhir kali kita ubah nilainya menjadi `index.main` yang artinya Lambda akan coba mencari file `index.js` dan memanggil method `main`. Sedangkan aplikasi kita sekarang menggunakan file `lambda.js` dan method yang perlu dipanggil adalah `handler`.

> **PENTING**: Selalu ingat hubungan antara Handler pada fungsi Lambda dan korelasinya dengan nama file dan nama method yang di-export.

Ya, kita harus mengganti konfigurasi Runtime Lambda.

1. Masuk pada halaman fungsi Lambda yang telah dibuat.
2. Pada tab **Code**, scroll ke bagian **Runtime settings** dan pilih **Edit**
3. Pada **Handler** isikan dengan **lambda.handler**
4. Pilih **Save**

Ini adalah isi dari file `lambda.js`

```js
const app = require('./src/index.js');
const serverless = require('serverless-http');

exports.handler = serverless(app);
```

Pada code diatas kita mengimpor object `app` dari file utama yaitu `index.js`. Aplikasi tidak melakukan bind ke port seperti pada file `local.js`. Namun hanya mengekspor sebuah fungsi pada atribut `handler`.

Kita memanfaatkan library [serverless-http](https://www.npmjs.com/package/serverless-http) untuk mengubah perilaku dari request/response Lambda ke bentuk HTTP request normal yang dimengerti oleh express.

Sekarang pada terminal di AWS Cloud9 coba ulangi request yang gagal tadi. Harusnya sekarang sudah bisa. Ganti URL dengan milik Anda sendiri.

```sh
curl -s -D /dev/stderr $INVOKE_URL/protected | jq .
```

```
HTTP/2 401 
date: Tue, 21 Feb 2023 05:23:16 GMT
content-type: application/json; charset=utf-8
content-length: 26
etag: W/"1a-pljHtlo127JYJR4E/RYOPb6ucbw"
x-powered-by: Express
apigw-requestid: BFgifgkoSQ0EPhw=

{
  "message": "Unauthorized"
}
```

Jika mendapat 401 maka API merespon dengan benar. Sekarang coba login ke API.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
$INVOKE_URL/login -d '
{
  "username": "workshop-user1",
  "password": "workshop123"
}' | jq .
```

```
HTTP/2 500 
date: Tue, 21 Feb 2023 05:26:34 GMT
content-type: application/json
content-length: 35
apigw-requestid: BFhBkioASQ0EPXQ=

{
  "message": "AccessDeniedException: User: arn:aws:sts::ACCOUNT_ID:assumed-role/serverless-todo-api-rioastamal-role-lgl95shr/serverless-todo-api-rioastamal is not authorized to perform: dynamodb:GetItem on resource: arn:aws:dynamodb:ap-southeast-1:ACCOUNT_ID:table/serverless-todo-rioastamal because no identity-based policy allows the dynamodb:GetItem action"
}
```

Ooops error apa lagi ini.

Dapat dilihat ternya kita memiliki masalah permission yaitu fungsi Lambda tidak memiliki permission untuk memanggil API **dynamodb:GetItem**. Kita akan memperbaiki masalah ini.

#### Menambahkan Permission ke Fungsi Lambda

Pada aplikasi Node.js yang dibuat tergantung pada beberapa layanan AWS yang lain seperti Amazon DynamoDB, Amazon SES, dan AWS Systems Manager. Cara yang direkomendasikan untuk memberikan permission adalah dengan konsep [_least-privilege_](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege).

Artinya permission atau hak ases hanya diberikan seperlunya saja, cukup hanya untuk fungsi tersebut dapat berjalan. Namun pada workshop ini kita akan memberikan permission yang sedikit melebar untuk mempermudah pemahaman.

Langkah untuk menambahkan permission pada fungsi Lambda.

1. Masuk pada halaman fungsi Lambda yang dibuat.
2. Pilih tab **Configuration**, pilih **Permissions**
3. Pada bagian **Execution role** terdapat **Role name** yang digunakan oleh fungsi Lambda kita.
4. Pilih role tersebut, contohnya **serverless-todo-api-rioastamal-role-pkqnzkp3**
![Execution role](https://user-images.githubusercontent.com/469847/224752239-a20c0757-9dd3-4152-ad64-a1dde9a92f84.png)

Anda akan dibawa ke halaman IAM untuk mengedit role. Pastikan Anda berada pada halaman **Summary** dari role ini.

1. Pada tab **Permissions** pilih **Add permissions**
![Function roles](https://user-images.githubusercontent.com/469847/224753234-d42de7bc-5b93-49c8-bb7d-c3ef972e1dfd.png)
2. Pilih **Attach policies**
3. Pada **Other permissions policies** ketik **dynamodb** lalu ENTER
4. Centang **AmazonDynamoDBFullAccess**, pilih **Clear filters**
5. Pada **Other permissions policies** ketik **ses** lalu ENTER
6. Centang **AmazonSESFullAccess**, pilih **Clear filters**
7. Pada **Other permissions policies** ketik **ssm** lalu ENTER
8. Centang **AmazonSSMReadOnlyAccess**, pilih **Clear filters**
9. Pilih **Add permissions**

![Permissions](https://user-images.githubusercontent.com/469847/222842504-8073651e-d387-4ad7-88c6-471ee26ebace.png)

Mari ulangi proses pemanggilan endpoint `/login` yang gagal sebelumnya.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
$INVOKE_URL/login -d '
{
  "username": "workshop-user1",
  "password": "workshop123"
}' | jq .
```

```
HTTP/2 200 
date: Tue, 21 Feb 2023 06:16:24 GMT
content-type: application/json; charset=utf-8
content-length: 232
x-powered-by: Express
etag: W/"e8-CccXfxVNNNmhFbddbNAfsvGxLxI"
apigw-requestid: BFoUvjRoyQ0EMBg=

{
  "token": "SOME_LONG_JWT_TOKEN"
}
```

Proses berhasil dan mengembalikan JWT token. Sekarang mari coba registrasi pengguna baru. 

> **PENTING**: Ganti [EMAIL_PENERIMA] dengan milik Anda. Pastikan email penerima adalah yang sudah di-verifikasi di Amazon SES sebelumnya.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
$INVOKE_URL/register -d '
{
  "username": "workshop-user2",
  "password": "workshop123",
  "fullname": "User Two",
  "email": "[EMAIL_PENERIMA]"
}' | jq .
```

```
HTTP/2 201 
date: Tue, 21 Feb 2023 06:21:20 GMT
content-type: application/json; charset=utf-8
content-length: 42
etag: W/"2a-nMoFx54+czTntmSLXl3mqIsZV4A"
x-powered-by: Express
apigw-requestid: BFpC_jblyQ0EMqg=

{
  "message": "User registered successfully"
}
```

Jika email juga masuk maka semua permission sudah benar.

  </details>
  <!--Deploy Code ke AWS Lambda-->

</details>
<hr>

<details>
  <summary><h2>Modul 4 - Membuat Frontend</h2></summary>

  <details>
    <summary><h3>Fork Repository serverless-todo-vue di GitHub</h3></summary>

Pada implementasi CI/CD frontend di AWS Amplify nantinya Anda akan menggunakan repository hasil fork dari serverless-todo-vue.

1. Pastikan Anda sudah login ke Akun GitHub Anda.
2. Buka repo [serverless-todo-vue](https://github.com/rioastamal-examples/serverless-todo-vue)
3. Pilih **Fork** di sisi kanan atas

![Fork button](https://user-images.githubusercontent.com/469847/222701103-77ae5f47-bdf8-4119-af09-f02888f8db8b.png)

4. Biarkan opsi lain sesuai nilai default, Pilih **Create fork**

![Create Fork](https://user-images.githubusercontent.com/469847/222701481-fbb50246-ebe4-42fd-940a-b8c8dc142a67.png)

  </details>
  <!--Fork Repository serverless-todo-vue di GitHub-->

  <details>
    <summary><h3>Hosting Frontend di AWS Amplify</h3></summary>

Aplikasi frontend dibuat menggunakan HTML/Javascript/Vue.js. Terdiri dari tiga file yaitu `index.html`, `login.htl`, dan `register.html`. Aplikasi dimodifikasi dari contoh [Vue.js TodoMVC](https://vuejs.org/examples/#todomvc).

Salah satu fitur utama AWS Amplify adalah hosting static website dan integrasi CI/CD secara otomatis dengan layanan version control seperti AWS CodeCommit atau GitHub.

Selain itu aplikasi akan ditempatkan di Content Delivery Network (CDN) terdekat dengan pengguna secara otomatis sehingga akses bisa lebih cepat.

Pada workshop ini kita akan menggunakan GitHub sebagai tempat menyimpan repository code frontend kita. Pastikan Anda memiliki akun GitHub untuk mengikuti proses ini.

#### Membuat App di Amplify Hosting

Pastikan Anda sudah melakukan fork repository serverless-vue-todo.

1. Masuk ke halaman [AWS Amplify](https://console.aws.amazon.com/amplify/home)
2. Pilih **GET STARTED**
![Amplify Get Started](https://user-images.githubusercontent.com/469847/224765306-67f1931f-c120-45f2-9a42-aebbd5462905.png)
3. Pada bagian Amplify Hosting pilih **Get started**
![Web App Get Started](https://user-images.githubusercontent.com/469847/224765332-6c745d3f-e6c6-457c-9fd5-3fa9a97a8a36.png)
4. Pilih **GitHub**, pilih **Continue**

Amplify Hosting memerlukan akses read pada repository yang ingin diintegrasikan. Akan muncul dialog bahwa AWS Amplify memerlukan permission untuk mengakses repository.

1. Pilih **Authorize AWS Amplify (ap-southeast-1)**
2. (Jika repository tidak muncul) Pilih **View GitHub permissions**, pilih akun GitHub tempat repository berada, pilih **All repositories**, pilih **Install & Authorize**
3. Pada halaman Add repository branch, pada Recently updated repositories pilih repository **serverless-todo-vue**
4. Pada **Branch** pilih **main**, pilih **Next**
![Add GitHub Repository](https://user-images.githubusercontent.com/469847/224766571-3dfc8144-cf18-4338-943e-36b06360902f.png)

Selanjutnya akan muncul halaman Build Settings dimana Anda dapat mengkonfigurasi bagaimana aplikasi di-build.

1. Pada **App name** isikan "serverless-todo-vue-{{NICKNAME}}", contoh milik saya **serverless-todo-vue-rioastamal**
2. Pada **Build and test settings** pilih **Edit** isikan dengan code build berikut.

```yaml
version: 1
frontend:
  phases:
    build:
      commands: 
        - bash build.sh
  artifacts:
    baseDirectory: build
    files:
      - '**/*'
  cache:
    paths: []
```

3. Buka **Advanced settings**, biarkan kosong pada **Build image** 
4. Masukkan environment variable berikut.
    - Key: `API_BASE_URL`, Value: `[HTTP_API_INVOKE_URL]` (pastikan untuk mengganti sesuai alamat HTTP API Anda di API Gateway, tanpa akhiran slash "/")
5. Pilih **Next**, pilih **Save and deploy**

Tunggu hingga proses build selesai ditandai dengan indikator _Provision_, _Build_, dan _Deploy_ berwarna hijau. Anda dapat memilih salah satu tersebut untuk melihat detil log dari masing-masing.

![Amplify Build Process](https://user-images.githubusercontent.com/469847/224769089-e05f9a2f-0cd6-403f-b269-a69d99d01944.png)

Proses build yang ada pada `build.sh` sangat sederhana karena hanya untuk mendemonstrasikan proses yang dapat dijalankan oleh Amplify Hosting ketika melakukan build aplikasi.

```sh
#!/bin/sh

[ -z "$API_BASE_URL" ] && {
    echo "Missing API_BASE_URL env." 2>&1
    exit 1
}

mkdir -p build/

echo "Frontend Build: Replacing API_BASE_URL with ${API_BASE_URL}..."
for _file in index.html login.html register.html
do
    sed "s#{{API_BASE_URL}}#$API_BASE_URL#g" $_file > build/$_file
done
```

Fungsi file ini hanya merewrite string `{{API_BASE_URL}}` dengan nilai yang disuplai dari environment variable. Kemudian menyalin file-file HTML folder `build/`.

  </details>
  <!--Hosting Frontend di AWS Amplify-->

  <details>
    <summary><h3>Mencoba Frontend Todo App</h3></summary>

Jika proses build selesai, pilih **main** untuk melihat detil dari proses build terakhir. Kemudian pilih link URL pada **Domain** untuk membuka aplikasi frontend.

![Amplify Link](https://user-images.githubusercontent.com/469847/224769818-55d45171-500c-4b9f-92bd-acfc77fb7975.png)

![Gambar halaman login](https://user-images.githubusercontent.com/469847/222702635-1635d84f-0a09-4f50-9277-0ded19a4e5ea.png)

Aplikasi akan meredirect ke halaman `/login.html` jika pengguna belum melakukan otentikasi. Sebelum mencoba login, aktifkan dulu debugger tools di web browser Anda dan buka tab Network.

Gunakan **username** "workshop-user1" dan **password** "workshop123".

![Login error](https://user-images.githubusercontent.com/469847/222702717-72d9cb31-49fc-49bc-8999-6e049b11c475.png)

Hal yang harusnya terjadi adalah proses otentikasi gagal. Jika Anda membuka Debugger tools pada web browser maka pesan error yang muncul karena malasah CORS.

#### Mengatasi CORS via Amazon API Gateway

Ada dua cara untuk mengatasi masalah CORS ini, Anda mengubah source code dari API untuk menambahkan CORS HTTP header atau melalui Amazon API Gateway. Kita akan menggunakan cara kedua.

1. Buka halaman [Amazon API Gateway](https://console.aws.amazon.com/apigateway/main/)
2. Pilih API yang telah dibuat sebelumnya, milik saya adalah **serverless-todo-gw-rioastamal**
3. Pada menu **Develop** sisi kiri pilih **CORS**, pilih **Configure**
4. Pada masing-masing **Access-Control-Allow-Origin** dan **Access-Control-Allow-Headers** isikan &quot;\*&quot; lalu pilih **Add**
5. Pada **Access-Control-Allow-Methods** pilih **\***
6. Pilih **Save**

![Konfigurasi CORS](https://user-images.githubusercontent.com/469847/222702848-3b78f73c-ea91-4f31-8b95-94ed115234c7.png)

Kembali ke halaman `/login.html` dan coba ulangi kembali prosesnya. Harusnya sekarang Anda bisa login.

#### Mencoba Membuat Todo Baru

Sekarang Anda harusnya berada pada halaman `/index.html`. Jika belum maka buka halaman tersebut. Ketika anda pertama kali mengakses Todo list ini maka aplikasi akan secara otomatis mencoba membuat Todo kosong dengan ID acak.

![Random Id](https://user-images.githubusercontent.com/469847/222704021-99f441bc-3a40-402e-800d-59d4d4f922ab.png)

Masih ingat pada langkah [Mencoba Endpoint PUT /todos/:id](#) Anda pernah membuat Todo via CLI di AWS Cloud9? Kita akan coba meng-_update_-nya. Pada contoh tersebut saya membuat todo dengan ID **rioastamal-1**.

1. Pada inputan **Create or update todos** masukkan **[TODO ID]** (ganti sesuai Todo ID yang Anda masukkan di langkah [mencoba endpoint PUT /todos/:id](#mencoba-endpoint-put-todosid), kemudian tekan ENTER
2. Akan muncul dua todo item yaitu &quot;Workshop Serverless&quot; dan &quot;Pulang makan&quot;, isikan item todo baru &quot;Tidur&quot; tekan ENTER

![Old Todo Id](https://user-images.githubusercontent.com/469847/222704718-ee8cdc08-8750-457c-896d-4e47ce50cefd.png)

![Todo Tidur](https://user-images.githubusercontent.com/469847/222705816-570a7e3c-a241-4e6a-a622-04c3c6f0f0f2.png)

Anda dapat masuk ke halaman [DynamoDB Table](https://console.aws.amazon.com/dynamodbv2/home/#table) yang dibuat sebelumnya untuk melihat data-data yang sudah dimasukkan.

### Menambahkan Link antar Halaman

Untuk mencoba implementasi CI/CD yang ada di Amplify Hosting kita akan mengubah sedikit tampilan dari halaman yang ada dengan menambahkan link di bagian bawah.

Pertama kita akan melakukan clone project serverless-todo-vue dari akun GitHub Anda masing-masing.

> **PENTING**: Clone dari fork repo Anda, bukan dari repo original rioastamal-examples/serverless-todo-vue.

1. Buka halaman project serverless-todo-vue yang telah anda fork di GitHub
2. Pilih **Code**, pada pilihan **Clone** di tab **Local** pastikan **SSH** terpilih
3. Copy repository URL versi SSH tersebut

![Clone from fork](https://user-images.githubusercontent.com/469847/222708678-bfa3b90f-bdce-4389-82a1-9f2f5028762d.png)

Masuk pada terminal di AWS Cloud9. Pastikan Anda berada pada direktori `~/environment`.

```sh
cd ~/environment
```

Gunakan perintah `git clone` berikut untuk meng-clone project. Sesuaikan dengan alamat repo Anda sendiri.

```sh
git clone git@github.com:[AKUN_GITHUB_ANDA]/serverless-todo-vue.git
```

```
Cloning into 'serverless-todo-vue'...
The authenticity of host 'github.com (20.205.243.166)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
ECDSA key fingerprint is MD5:7b:99:81:1e:4c:91:a5:0d:5a:2e:2e:80:13:3f:24:ca.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,20.205.243.166' (ECDSA) to the list of known hosts.
remote: Enumerating objects: 33, done.
remote: Counting objects: 100% (33/33), done.
remote: Compressing objects: 100% (20/20), done.
remote: Total 33 (delta 14), reused 28 (delta 9), pack-reused 0
Receiving objects: 100% (33/33), 8.33 KiB | 8.33 MiB/s, done.
Resolving deltas: 100% (14/14), done.
```

#### File login.html

Buka file `login.html` pada Cloud9 dan tambahkan link ke halaman `/register.html` dengan menghilangkan komentar sekitar baris 30.

![Edit File login.html](https://user-images.githubusercontent.com/469847/224772352-15bb598d-e211-4f66-b1b7-891d109655e2.png)

```html
<section class="main">
  <p><a href="register.html">Register</a></p>
</section>
```

Menjadi seperti berikut.

```html
<section class="main">
  <p><a href="register.html">Register</a></p>
</section>
```

#### File register.html

Buka file `register.html` di Cloud9 dan tambahkan link ke halaman `/login.html` dengan menghilangkan komentar sekitar baris 48.

```html
<!-- BEGIN - Remove this line
<section class="main">
  <p><a href="login.html">Login</a></p>
</section>
END - Remove this line -->
```

Menjadi seperti berikut

```html
<section class="main">
  <p><a href="login.html">Login</a></p>
</section>
```

#### File index.html

Buka file `index.html` dan tambahkan link ke halaman `/login.html?logout` dengan menghilangkan komentar sekitar baris 300.

```html
<!-- BEGIN - Remove this line
<p><a href="login.html?logout">Logout</a></p>
END - Remove this line -->
```

Menjadi seperti berikut.

```html
<p><a href="login.html?logout">Logout</a></p>
```

#### Commit Perubahan

Pastikan anda sudah berada dalam project root direktori dari `serverless-todo-vue`.

```sh
cd ~/environment/serverless-todo-vue/
```

Harusnya sekarang terdapat tiga file yang telah dimodifikasi dan siap di-commit.

```sh
git status
```

```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html
        modified:   login.html
        modified:   register.html

no changes added to commit (use "git add" and/or "git commit -a")
```

Lakukan `git commit` untuk menyimpan perubahan.

```sh
git add index.html login.html register.html
```

Kemudian.

```sh
git commit -m "Add links at the bottom of each page"
```

Push perubahan ke GitHub repository.

```sh
git push origin main
```

Ketika push sukses dilakukan sekarang coba masuk ke halaman [AWS Amplify](console.aws.amazon.com/amplify/home). Harusnya proses build sedang berjalan.

![Amplify Build](https://user-images.githubusercontent.com/469847/224782010-8534391a-b03d-4b36-a8ff-f773e57c4473.png)

Hal ini menunjukkan AWS Amplify mendeteksi perubahan dan melakukan build secara otomatis. Dengan ini Anda tidak perlu mengelola CI/CD server sendiri untuk frontend.

Ketika build selesai coba kembali buka halaman aplikasi Frontend untuk memastikan bahwa perubahan telah ter-deploy dengan sempurna. Hal itu ditandai dengan adanya link dibagian bawah setiap halaman.

![New HTML page deployed](https://user-images.githubusercontent.com/469847/224783017-0ebbd4af-1e9f-42d1-bfb3-a1e3ba47fbc7.png)

  </details>
  <!--Mencoba Frontend Todo App-->

</details>
<hr>

<details>
  <summary><h2>Modul 5 - Memecah Layanan (Decoupling)</h2></summary>
  
  <details>
    <summary><h3>Decoupling Proses Registrasi</h3></summary>

Jika Anda perhatikan proses registrasi masih terikat dengan proses lain yang sebenarnya bisa dipisah yaitu proses pengiriman email. 

Bayangkan jika layanan email bermasalah maka proses proses registrasi dianggap gagal oleh pengguna akhir. Padahal proses pengiriman sebenarnya bisa menunggu beberapa saat setelah registrasi selesai.

Untuk melakukan decoupling bisa digunakan queue dan proses registrasi akan mengirim pesan ke queue. Selanjutnya pesan di queue akan diproses worker yang bertugas untuk mengirimkan email.

Worker untuk mengirim email nantinya akan dijalankan pada AWS Lambda dan Amazon SQS untuk menyimpan queue. 

Kita dapat mengintegrasikan Amazon SQS dengan AWS Lambda dengan mudah. Dimana jika terdapat pesan baru masuk ke queue di SQS, maka pesan tersebut dapat diteruskan ke sebuah fungsi Lambda.

![serverless-workshop-decoupled](https://user-images.githubusercontent.com/469847/224513755-ab4be3e3-2cbb-4aae-8106-c93ce8a036b6.png)

> Decoupling pengiriman email dari API registrasi

  </details>
  <!--Decoupling Proses Registrasi-->

  <details>
    <summary><h3>Membuat SQS queue</h3></summary>

Kita akan menggunakan Amazon SQS untuk melakukan decoupling API dari task yang tidak harus selesai saat itu juga. Dalam hal ini task pengiriman welcome email. 

Task ini tidak harus langsung selesai ketika API registration dipanggil dan ada kemungkinan proses pengiriman email lambat. Sehingga task ini kandidat yang cocok untuk dikirim ke queue.

1. Masuk pada halaman [Amazon SQS](https://console.aws.amazon.com/sqs/home) 
2. Pilih **Create queue**
![Create SQS queue](https://user-images.githubusercontent.com/469847/224784309-83695da3-f0f3-4a45-812d-29625060fddd.png)
3. Pada pilihan **Type** pilih **Standard** dan pada **Name** isikan &quot;serverless-todo-welcome-email-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-welcome-email-rioastamal**
4. Sisanya biarkan sesuai nilai bawaan, kemudian pilih **Create queue**

Nantinya queue ini akan digunakan untuk menyimpan pesan yang dikirimkan oleh API ketika user baru saja mendaftar.

Kemudian pesan ini akan diproses oleh worker dalam hal ini adalah sebuah fungsi Lambda.
  
  </details>
  <!--Membuat SQS queue-->

  <details>
    <summary><h3>Membuat Welcome Email Worker dengan AWS Lambda</h3></summary>

Fungsi Lambda ini bertugas untuk memproses pesan yang ada di queue yang dikirim oleh proses registrasi.

1. Masuk pada halaman [AWS Lambda](https://console.aws.amazon.com/lambda/home). kemudian halaman **Functions**, pilih **Create function**
2. Pilih **Author from scratch**
3. Pada **Function name** isikan &quot;serverless-todo-email-worker-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-email-worker-rioastamal**
4. Pada **Runtime** pilih **Node.js 16.x** kemudian **Architecture** pilih **x86_64**
5. Pada **Change default execution role**, pilih **Use an existing role**, pada **Existing role** pilih role yang sebelumnya dibuat, contoh milik saya **service-role/serverles-todo-api-rioastamal-role-RANDOM**
6. Sisanya biarkan sesuai nilai bawaan, kemudian pilih **Create function**

![Existing role](https://user-images.githubusercontent.com/469847/222845182-9432c93b-1d6c-4121-85a5-d17d12f6ea1f.png)

Kita memilih role yang sebelumnya agar kita tidak perlu melakukan setup ulang permission. Pada kasus production Anda harusnya hanya memberikan permission yang diperlukan saja.

Selanjutnya kita akan mengkonfigurasi environment variable yang diperlukan.

1. Pada fungsi Lambda yang dibuat, pilih tab **Configuration**, pilih **Environment variables**
2. Pilih **Edit**, pilih **Add environment variable**, tambahkan environment variable berikut tapi sesuaikan dengan milik Anda.
   - **Key**: `APP_TABLE_NAME`, **Value**: `serverless-todo-{{NICKNAME}}`
   - **Key**: `APP_URL`, **Value**: `{{AMPLIFY_URL}}` (URL dari Amplify Hosting untuk aplikasi frontend)
   - **Key**: `APP_FROM_EMAIL_ADDR`, **Value**: `EMAIL.ANDA+sender@gmail.com` (Ganti dengan email pengirim yang telah diverifikasi di Amazon SES)
3. Pilih **Save**

Fungsi Lambda ini perlu untuk mengakses Amazon SQS jadi permission perlu ditambahkan pada _execution role_.

1. Masih pada tab **Configuration**, pilih **Permissions**
2. Pada **Execution Role** pilih **Role name** untuk membuka halaman IAM

Kita akan menambahkan permission agar fungsi Lambda dapat mengakses Amazon SQS.

3. Pilih **Add Permissions**, pilih **Attach policies**
4. Pada pencarian isikan &quot;SQS&quot; kemudian ENTER
5. Centang **AWSLambdaSQSQueueExecutionRole** 
6. Pilih **Add permissions**

![New SQS permission](https://user-images.githubusercontent.com/469847/222846137-4ddd99e2-4fb5-4298-a6cd-2299a72f17b0.png)

Langkah berikutnya adalah integrasi SQS queue dengan fungsi Lambda.

1. Masih pada tab **Configuration**, pilih **Triggers*, pilih **Add trigger**
2. Pada **Trigger configuration** ketik &quot;SQS&quot; lalu pilih **SQS**
3. Pada **SQS queue** pilih queue yang telah dibuat sebelumnya, milik saya adalah **serverless-todo-welcome-email-rioastamal**
![Add SQS Trigger](https://user-images.githubusercontent.com/469847/224788078-551e2d96-29cf-41b2-b1a5-12a13b6c6444.png)
4. Biarkan isian sisanya sesuai bawaan, pilih **Add**

#### Clone Email Worker Repo

Selanjutnya clone repository [serverless-todo-email-worker](https://github.com/rioastamal-examples/serverless-todo-email-worker). Masuk ke sesi terminal pada AWS Cloud9 pastian Anda berada pada `~/environment`.

```sh
cd ~/environment
```

Kemudian lakukan clone.

```sh
git clone git@github.com:rioastamal-examples/serverless-todo-email-worker.git
```

Masuk pada direktori `serverless-todo-email-worker` dan install semua dependencies

```sh
cd serverless-todo-email-worker
```

```sh
npm install --omit=dev
```

Proses registrasi nantinya mengirim pesan ke queue dengan format JSON seperti berikut:

```json
{ 
  "username": "USERNAME"
}
```

Worker kemudian akan mengambil pesan tersebut dan melakukan query ke DynamoDB untuk mendapatkan detil dari username `USERNAME` seperti `fullname` dan `email`. Setelah mendapatkan data tersebut maka worker akan mengirim email menggunakan Amazon SES.

#### Mencoba Worker di Lokal

Berbeda dengan code di `serverless-todo-api` pada worker ini 100% dibuat khusus untuk dijalankan di Lambda sehingga tidak ada proses binding port dan sebagainya. Dapat dilihat pada `src/handlers/main.js`.

Untuk menjalankan di lokal Anda perlu memanggil file `local.js`. File ini akan membaca file `event-sqs.sample.json` yang mensimulasikan event dari SQS.

Kita akan mencoba mengirim ulang email registrasi ke username `workshop-user1`. Ada beberapa environment variable yang perlu diset yaitu.

- `APP_TABLE_NAME` nama tabel di DynamoDB
- `APP_FROM_EMAIL_ADDR` email pengirim yang verified di Amazon SES
- `APP_URL` URL frontend di Amplify Hosting
- `APP_DUMMY_SQS_BODY` simulasi data yang dikirim (hanya untuk eksekusi lokal saja)

Sesuaikan nilainya dengan milik Anda.

```sh
export APP_TABLE_NAME=serverless-todo-{{NICKNAME}} \
APP_URL={{AMPLIFY_URL}} \
APP_FROM_EMAIL_ADDR=EMAIL.ANDA+sender@gmail.com \
APP_DUMMY_SQS_BODY='{ "username": "workshop-user1" }'
```

```sh
node local.js
```

```
...
mailResponse {
  '$metadata': {
    httpStatusCode: 200,
    requestId: '336fa895-4124-4f52-b4bb-e23c42d6616f',
    extendedRequestId: undefined,
    cfId: undefined,
    attempts: 1,
    totalRetryDelay: 0
  },
  MessageId: '010e0186dc260d9c-402d537b-7d67-48bc-8763-3eebac2aa6e7-000000'
}
Email sent
```

Jika sukses maka harusnya anda menerima email. Cek inbox email yang digunakan oleh `workshop-user1`.

Pada fungsi email worker Lambda ini kita menambahkan beberapa metadata pada isi dari email.

![Email from worker](https://user-images.githubusercontent.com/469847/224790566-728be554-82e0-454d-8f9d-5748a6eaf400.png)

#### Deploy Email Worker ke Fungsi Lambda

Script akan kita paket menjadi zip dan upload ke S3 bucket, kemudian kita impor ke fungsi AWS Lambda yang dibuat.

Pastikan masih berada pada root directory `serverless-todo-email-worker`. Jalankan script `build.sh` dan set nilai dari environment variable `APP_FUNCTION_BUCKET` sesuai dengan milik Anda.

> **PENTING**: Ganti [NAMA_BUCKET] dengan S3 bucket yang Anda buat diawal

```sh
export APP_FUNCTION_BUCKET=[NAMA_BUCKET]
```

```sh
bash build.sh
```

Berikutnya kita akan mengupload zip dari bucket tersebut ke fungsi Lambda.

1. Masuk ke console [AWS Lambda](https://console.aws.amazon.com/lambda/home)
2. Pilih **Functions** pilih fungsi yang telah dibuat
3. Pada tab **Code**, pilih **Upload from**, pilih **Amazon S3 location**
4. Pada **Amazon S3 link URL** isikan dari Object URL dari **serverless-todo-email-worker.zip** yang barusan di upload. Anda dapat melihatnya lewat halaman Amazon S3.

#### Memasukkan Environment Variable pada Fungsi Lambda

Berikutnya memasukkan nilai ke environment variable yang diperlukan API.

1. Masih dihalaman fungsi Lambda email worker
2. Pilih tab **Configuration**, pilih **Environment variables**, pilih **Edit**
3. Pilih **Add environment variable**, masukkan nilai sesuai milik Anda:
   - Key: `APP_TABLE_NAME`, Value: `serverless-todo-{{NICKNAME}}`
   - Key: `APP_URL`, Value: `[Amplify Hosting URL]`
   - Key: `APP_FROM_EMAIL_ADDR`, Value: `EMAIL.ANDA+sender@gmail.com`
4. Pilih **Save**

Perlu diingat fungsi utama dari worker email adalah file `src/handlers/main.js` dan nama fungsinya `welcomeEmailSender`. Sehingga Anda perlu mengubah konfigurasi Handler.

1. Masih dihalaman fungsi Lambda email worker
2. Pada tab **Code** bagian **Runtime settings** pilih **Edit**
3. Pada **Handler** isikan **src/handlers/main.welcomeEmailSender**
4. Pilih **Save**

#### Mencoba Email Worker lewat Test event

Masuk ke halaman fungsi Lambda email worker yang dibuat, milik saya adalah **serverless-todo-email-worker-rioastamal**.

1. Pilih tab **Test**
2. Scroll ke **Even JSON**, masukkan JSON berikut

```json
{
    "Records": [
        {
            "messageId": "059f36b4-87a3-44ab-83d2-661975830a7d",
            "receiptHandle": "AQEBwJnKyrHigUMZj6rYigCgxlaS3SLy0a...",
            "body": "{ \"username\": \"workshop-user1\" }",
            "attributes": {
                "ApproximateReceiveCount": "1",
                "SentTimestamp": "1545082649183",
                "SenderId": "AIDAIENQZJOLO23YVJ4VO",
                "ApproximateFirstReceiveTimestamp": "1545082649185"
            },
            "messageAttributes": {},
            "md5OfBody": "098f6bcd4621d373cade4e832627b4f6",
            "eventSource": "aws:sqs",
            "eventSourceARN": "arn:aws:sqs:us-east-2:123456789012:my-queue",
            "awsRegion": "ap-southeast-1"
        }
    ]
}
```

3. Pilih **Test** tanpa perlu save
4. Pilih **Details** untuk melihat output dari execution

Jika muncul tulisan **Message sent to workshop-user1** berarti proses berjalan dengan sukses. Cek inbox email Anda, email ini dikirim kepada username `workshop-user1` yang dibuat pada langkah-langkah yang lalu.

  </details>
  <!--Membuat Welcome Email Worker dengan AWS Lambda-->

  <details>
    <summary><h3>Decoupling Email dari API /register</h3></summary>

Proses decoupling pada sisi API registrasi di project `serverless-todo-api` adalah membuang code proses pengiriman email dan menggantinya dengan queue.

Pada workshop ini saya sudah menyiapkan code yang sudah jadi di branch yang berbeda yaitu `decoupled-welcome-email`. 

Pada terminal di AWS Cloud9 gunakan perintah berikut untuk melihat remote branch di project `serverless-todo-express-api`.

```sh
cd ~/environment/serverless-todo-express-api
```

```sh
git branch -a
```

```
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/decoupled-welcome-email
  remotes/origin/main
```

Buat branch baru bernama `decoupled-welcome-email` dan masuk pada branch tersebut.

```sh
git checkout -b decoupled-welcome-email origin/decoupled-welcome-email
```

Sekarang harusnya ada dua branch di local. Branch yang aktif adalah `decoupled-welcome-email` ditandai dengan adanya `*`.

```sh
git branch
```

```
* decoupled-welcome-email
  main
```

Coba buka kembali file `src/index.js` pada AWS Cloud9 maka akan ada perbedaan dengan sebelumnya. Dimana sekarang fungsi `sendWelcomeEmail` bukan mengirim email langsung melainkan hanya mengirim pesan ke SQS queue.

```javascript
async function sendWelcomeEmail(username)
{
  const queueResponse = await sqsclient.send(new SendMessageCommand({
    QueueUrl: sqsQueueUrl,
    MessageBody: JSON.stringify(({ username: username }))
  }));
  
  console.log('queueResponse', queueResponse);
}
```

#### Deploy Ulang Fungsi Lambda untuk Backend

Pastikan masih berada pada branch `decoupled-welcome-email`. Jalankan perintah untuk build dan upload zip ke S3 bucket. Sesuaikan nama bucket milik Anda.

> **PENTING**: Ganti [NAMA_BUCKET] dengan bucket milik Anda yang dibuat sebelumnya.

```sh
export APP_FUNCTION_BUCKET=[NAMA_BUCKET]
```

```
bash build.sh
```

Setelah sukses diupload, lanjutkan dengan mengimpor file zip tersebut.

1. Masuk ke halaman [AWS Lambda](https://console.aws.amazon.com/lambda/home), pilih fungsi serverless-todo-api-{{NICKNAME}}
2. Pilih tab **Code**, pilih **Upload from**, pilih **Amazon S3 location**
3. Pada **Amazon S3 link URL** isikan dari nilai dari Object URL `serverless-todo-api.zip` yang ada di S3 bucket. (Masuk pada halaman S3 bucket untuk menyalin nilainya)

Selanjutnya adalah menambah environment variable baru di fungsi Lambda. Yaitu `APP_SQS_URL`, nilai URL dapat Anda lihat pada halaman SQS queue yang telah dibuat sebelumnya.

1. Masih pada halaman fungsi Lambda yang sama
2. Pilih tab **Configuration**, pilih **Environment variables**
3. Pilih **Edit** dan tambahkan environment variable berikut.
   - **Key**: `APP_SQS_URL`, **Value**: `[SQS_URL]` (Ganti [SQS_URL] dengan milik Anda)
4. Pilih **Save**

![SQS URL](https://user-images.githubusercontent.com/469847/222715597-682bd709-ec0e-426a-a9c7-f953b93c6243.png)

Sekarang harusnya proses decoupling pengiriman welcome email dan registrasi selesai.

Buka kembali halaman frontend.

1. Navigasi ke `/login.html?logout` untuk menghapus token di local storage
2. Navigasi ke `/register.html` untuk mencoba melakukan registrasi user dengan backend yang sudah decoupling dari welcome email.

Jika semua berjalan sukses, maka harusnya data user masuk ke DynamoDB dan email juga terkirim.

<blockquote>
<b>Atau terjadi error? Jika terjadi error maka tugas Anda untuk memperbaikinya :).</b>

Petunjuk:  
  
- Buka network debugger di web browser atau coba lihat logs dari fungsi Lambda untuk mengetahui error yang terjadi
- Tambahkan permission yang diperlukan
</blockquote>

  </details>
  <!--Decoupling Email dari Backend-->
</details>
<hr>

<details>
  <summary><h2>Penutup</h2></summary>

Selamat Anda telah menyelesaikan workshop &quot;Membangun Backend dan Frontend dengan Arsitektur Serverless&quot;. 

Pada workshop ini Anda telah mempelajari bagaimana memanfaatkan layanan-layanan AWS untuk deployment aplikasi backend dan frontend. AWS Lambda untuk menjalankan code, Amazon DynamoDB sebagai NoSQL database, AWS Amplify untuk hosting frontend, Amazon SES untuk mengirim email, Amazon API Gateway sebagai proxy/router, Amazon SQS untuk menyimpan queue dan AWS Systems Manager untuk menyimpan parameter bersifat secret.

Dengan serverless Anda dapat tidak perlu memikirkan tentang pengelolaan server. Serverless memiliki karakteristik auto-scaling, memiliki high availibility, dan scale-to-zero sehingga biaya lebih rendah.

Sehingga memungkinkan Anda untuk fokus ke aplikasi dan memungkinkan untuk rilis dan mendapatkan feedback lebih cepat.

### Pembersihan

Jika Anda menjalankan workshop ini menggunakan akun Anda sendiri maka Anda perlu menghapus resource yang telah dibuat untuk menghindari adanya tagihan biaya.

Anda dapat masuk ke masing-masing halaman layanan untuk menghapusnya. Contohnya masuk Amazon S3 dan menghapus file-file yang ada pada bucket yang digunakan pada workshop ini.

Berikut adalah beberapa resource yang dibuat pada workshop ini.

- S3 bucket
- DynamoDB table
- Parameter Store di AWS Systems Manager
- Lambda function
- Verified identity di Amazon SES
- SQS queue
- App di AWS Amplify
- SSH Public key di GitHub

</details>