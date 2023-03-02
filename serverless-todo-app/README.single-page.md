<a name="top"></a>

<!-- begin step-0 -->

## Workshop Serverless Todo App

Pada workshop ini peserta akan membuat sebuah aplikasi todo yang dibangun dengan arsitektur serverless. Backend pada aplikasi ditulis dengan Node.js dan frontend ditulis dengan HTML/Javascript/Vue.js.

Pada backend layanan yang digunakan adalah:

- AWS Lambda untuk menjalankan kode Node.js
- Amazon API Gateway untuk proxy atau routing ke API
- Amazon DynamoDB untuk database (NoSQL)
- Amazon SQS untuk queue
- Amazon Systems Manager untuk menyimpan parameter secret
- Amazon S3 untuk menyimpan zip dari fungsi Lambda sebelum deployment

Pada frontend layanan yang digunakan adalah:

- AWS Amplify untuk hosting static website HTML/Javascript/Vue.js

Layanan lain yang digunakan adalah:

- GitHub sebagai Git repository untuk menyimpan kode yang digunakan pada workshop

<!-- end step-0 -->

### Upload Public SSH Key ke GitHub

Masih pada terminal di AWS Cloud9. Buat SSH key baru.

```
ssh-keygen
```

Kosongsi saja password dan langsung tekan ENTER.

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

Salin isi dari `~/.ssh/id_rsa.pub`.

```sh
cat ~/.ssh/id_rsa.pub
```

Kita akan memasukkan public key tersebut ke akun GitHub.

1. Buka akun GitHub Anda masuk ke **Settings**, 
2. Pilih **SSH and GPG keys**, pilih **New SSH key**
3. Pada title isikan &quot;awsug-workshop-cloud9&quot;
4. Pada **Key type** pilih **Authentication Key**
5. Paste isi dari `~/.ssh/id_rsa.pub` ke inputan **Key**
6. Pilih **Add SSH key**

Setelah proses selesai harusnya Anda dapat melakukan push ke repository pada akun GitHub Anda.

### Membuat S3 Bucket

Bucket ini akan digunakan untuk menyimpan kode fungsi Lambda yang kemudian akan dideploy lewat halaman console AWS Lambda.

1. Masuk pada halaman Amazon S3. Anda dapat melakukannya lewat inputan _Search_ disisi atas AWS console lalu ketik &quot;S3&quot; - pilih **Amazon S3** - pilih **Create bucket**
2. Pada **Bucket name** isikan &quot;serverless-workshop-{{YYMM}}-{{NICKNAME}}&quot;. 
    - Ganti {{YYYYMM}} dengan tahun bulan, misal untuk Maret 2023 gunakan **202303**.
    - Ganti {{NICKNAME}} dengan nama anda atau sesuatu yang unik. Hanya inputkan alphanuric saja, contoh jika nama saya Rio Astamal maka gunakan **rioastamal**.
    - Contoh lengkap untuk nama S3 Bucket **serverless-workshop-202303-rioastamal**
3. Pada **AWS Region** pilih _Asia Pacific (Singapore) ap-southeast-1_
4. Biarkan opsi lainnya dengan nilai bawaan, kemudian pilih tombol **Create bucket**

Harusnya sekarang Anda memiliki bucket baru, contoh milik saya **serverless-workshop-202303-rioastamal**.

### Membuat DynamoDB Table

Kita akan menggunakan Amazon DynamoDB untuk menyimpan data user dan Todo list. Untuk itu Anda perlu membuat sebuah DynamoDB Table baru.

Disini kita hanya menggunakan satu tabel saja dan menerapkan konsep Single Table Design pada DynamoDB.

1. Masuk pada halaman Amazon DynamoDB. Anda dapat melakukannya lewat inputan _Search_ disisi atas AWS console lalu ketik &quot;dynamodb&quot;, pilih **Amazon DynamoDB**, pilih **Create table**
2. Pada **Table name** isikan &quot;serverless-todo-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-rioastamal**
3. Pada **Partition key** isikan &quot;pk&quot; dengan tipe **String**
4. Pada **Sort key** isikan &quot;sk&quot; dengan tipe **String**
4. Biarkan opsi lainnya dengan nilai bawaan, kemudian pilih tombol **Create table**

Tunggu beberapa saat maka tabel akan siap. Itu ditandai dengan status dari tabel yaitu **Active**.

### Membuat Fungsi Lambda

Kita akan membuat sebuah fungsi pada AWS Lambda untuk menjalankan aplikasi yang ditulis dengan Node.js. Runtime Node.js adalah salah satu official runtime yang didukung oleh Lambda.

Fungsi ini akan kita integrasikan dengan Amazon API Gateway sebagai proxy/gateway agar bisa diakses dari internet.

1. Masuk pada halaman AWS Lambda. Anda dapat melakukannya lewat inputan _Search_ disisi atas AWS console lalu ketik &quot;lambda&quot; pilih **AWS Lambda**, pilih **Create a function**
2. Pada **Function name** isikan &quot;serverless-todo-api-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-api-rioastamal**
3. Pada **Runtime** pilih **Node.js 16.x** kemudian **Architecture** pilih **x86_64**
4. Sisanya biarkan sesuai nilai bawaan, kemudian pilih **Create function**

Sekarang sebuah fungsi Lambda telah dibuat. Kita akan mencoba menjalankan fungsi tersebut.

#### Membuat Test event

Sebuah fungsi Lambda dieksekusi ketika ada sebuah trigger event tertentu. Kita akan mensimulasikan trigger dari sebuah event yang dikirim oleh API Gateway.

1. Pada tab menu pilih **Test** kemudian akan tampil konfigurasi **Test event**.
2. Pada **Test event action** pilih **Create new event**
3. Pada **Event name** isikan **api-gateway-proxy**
4. Pada **Event sharing settings** pilih **Private**
5. Pada **Template** pilih **API Gateway AWS Proxy**
6. pilih tombol **Save** lalu **Test**

Pada bagian **Execution result** akan muncul output berupa JSON string dari code Node.js yang dijalankan oleh Lambda.

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

Simpan code tersebut pilih **Deploy** kemudian **Test**. Harusnya respon yang didapat adalah sebuah error.

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

Hal itu karena handler fungsi Lambda tersebut dikonfigurasi dengan nilai `index.handler`. Artinya AWS Lambda akan menjalankan fungsi `handler` pada file `index.js`. Kita telah mengganti fungsinya dari `handler` ke `main` sehingga error tersebut terjadi.

Untuk mengatasinya ubah konfigurasi handler dari fungsi Lambda ini.

1. Pada tab **Code** scroll ke bagian **Runtime settings** lalu pilih **Edit**
2. Pada bagian **Handler** ganti nilai dari `index.handler` menjadi `index.main`
3. Pilih tombol **Save** lalu kembali pilih tombol **Test**

Harusnya sekarang fungsi berjalan normal dan mengembalikan output sesusai dengan isi dari test event **api-gateway-proxy** pada atribut `body`.

### Menghubungkan Fungsi Lambda ke Amazon API Gateway

Amazon API Gateway akan bertindak sebagai router yang mengarahkan request ke fungsi Lambda yang dibuat.

1. Masuk pada halaman Amazon API Gateway. Anda dapat melakukannya lewat inputan _Search_ AWS console lalu ketik &quot;api gateway&quot; - pilih **API Gateway** 
2. Pada **Choose an API type** pilih **HTTP API** kemudian pilih **Build**

Konfigurasi berikut akan menghubungkan fungsi Lambda yang dibuat dengan sebuah HTTP API.

1. Pada **Integrations** pilih **Add Integration**, pilih **Lambda**, **AWS Region** pilih **ap-southeast-1**, **Lambda function** pilih fungsi Lambda yang telah dibuat, **Version** pilih 2.0
2. Pada **API name** isikan &quot;serverless-todo-gw-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-gw-rioastamal**, pilih **Next**
3. Kemudian pada bagian routing **Method** pilih **ANY**, **Resource Path** masukkan **/{proxy+}**, **Integration target** pilih fungsi Lambda Anda, pada kasus saya adalah **serverless-todo-api-rioastamal**
4. Pada konfigurasi stage, pada **Stage name** pilih **$default** dan pastikan **Auto-deploy** aktif
5. Pada halaman review jika sudah sesuai, pilih **Create**

Anda akan dibawa pada halaman detil dari HTTP API. Lihat pada bagian Stage  terdapat **Invoke URL** yang merupakan alamat dari HTTP API. Buka link tersebut untuk mengeksekusi fungsi Lambda yang baru dibuat.

Outputnya adalah JSON string yang isinya adalah request yang dikirimkan oleh Amazon API Gateway. Milik saya outputnya seperti berikut.

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

Aplikasi ini masih bersifat monolith jadi route path `/{proxy+}` berfunsi sebagai catch-all route sehingga path apapun akan ditangkap dan diteruskan ke target yang ditentukan dalam hal ini fungsi Lambda Anda.

### Membuat Identity di Amazon SES

Untuk dapat mengirim email di Amazon SES maka diperlukan identity. Identity ini digunakan ketika proses pengiriman email. Bisa berupa verifikasi domain, subdomain atau email.

Ketika akun masih berada pada Sandbox maka alamat penerima juga perlu kita masukkan ke verified identity.

Pada langkah ini kita akan membuat dua verified identity email, satu untuk pengirim dan satu untuk penerima. Kita akan memanfaatkan tanda plus **+** pada alamat email untuk membuat alias.

#### Membuat Identity untuk Pengirim

1. Masuk pada halaman [Amazon SES](https://console.aws.amazon.com/ses/home#/homepage), pilih **Create identity** 
2. Pada **Identity type** pilih **Email address**
3. Pada **Email address** isikan &quot;{{EMAIL_ANDA}}+sender@example.com&quot;, contoh adalah **john+sender@gmail.com**
4. Pilih **Create identity**

Anda akan menerima email verifikasi dari Amazon SES. Klik link verifikasi tersebut untuk memvalidasi identity dari email pengirim.

#### Membuat Identity untuk Penerima

1. Pada halaman [Amazon SES](https://console.aws.amazon.com/ses/home#/homepage), pilih **Create identity** 
2. Pada **Identity type** pilih **Email address**
3. Pada **Email address** isikan &quot;{{EMAIL_ANDA}}+receiver@example.com&quot;, contoh adalah **john+receiver@gmail.com**
4. Pilih **Create identity**

Cek email Anda untuk link verifikasi. Setelah proses verifikasi selesai harusnya Anda memiliki dua verified identity dari satu alamat email.

### Membuat Parameter di AWS Systems Manager

API menggunakan JWT untuk proses otentikasi. Dalam proses pembuatan JWT token diperlukan nilai _secret_ untuk proses enkripsi. _Secret_ ini bisa saja diletakkan di environment variable namun cara yang lebih aman adalah menyimpan dan mengenkripsi nilainya ditempat terpisah.

Untuk itu digunakan AWS Systems Manager Parameter Store.

1. Masuk pada halaman [Amazon Systems Manager](https://console.aws.amazon.com/systems-manager/home)
2. Pada menu **Application Management** pilih **Parameter Store** kemudian **Create parameter**
3. Pada name isikan &quot;/{{NICKNAME}}/serverless-todo/development/jwt-secret&quot; contoh milik saya **/rioastamal/serverless-todo/development/jwt-secret**
4. Pada **Tier** pilih **Standard**
5. Pada **type** pilih **SecureString**, biarkan opsi lain sesuai bawaan, pada **Value** isikan &quot;workshop-serverless-todo-123456&quot;
6. Pilih **Create parameter**

Kita akan mengambil dan menggunakan nilai parameter pada code API.

### Menjalankan Todo API di AWS Cloud9

Menjalankan Todo API di AWS Cloud9 sama halnya kita menjalankannya di mesin lokal. Kita akan melakukan clone project todo api yang telah disiapkan.

1. Masuk pada halaman [AWS Cloud9](https://console.aws.amazon.com/cloud9/home)
2. Pilih **Open** untuk membuka environment yang telah ada sebelumnya
3. Buka terminal baru jika belum ada, pastikan berada pada `/home/ec2-user/environment`

```sh
cd ~/environment
```

4. Clone project Serverless Todo API

```sh
git clone https://github.com/rioastamal-examples/serverless-todo-express-api.git
```

5. Masuk pada direktori project dan install dependencies menggunakan `npm`

```sh
cd serverless-todo-express-api
npm install --omit=dev
```

Untuk menjalankan API kita butuh menyuplai beberapa environment variable yaitu:
- `APP_TABLE_NAME`: DynamoDB table
- `APP_PARAMSTORE_JWT_SECRET_NAME`: nama Parameter Store untuk jwt-secret
- `APP_FROM_EMAIL_ADDR`: alamat email pengirim yang sudah diverifikasi di Amazon SES

Kembali pada terminal di AWS Cloud9, pastikan Anda berada pada root direktori project jalankan file `local.js`. 

> **PENTING**: Sesuaikan nilai dari setiap environment variable ini dengan milik Anda.

```sh
export APP_TABLE_NAME=serverless-todo-rioastamal
export APP_PARAMSTORE_JWT_SECRET_NAME=/rioastamal/serverless-todo/development/jwt-secret
export APP_FROM_EMAIL_ADDR=EMAIL.SAYA+sender@gmail.com
```

```sh
node local.js
```

```
API server running on port 8080
```

Buka terminal session baru pada AWS Cloud9 dengan memilih tanda plus **+** kemudian pilih **New Terminal**. Pada terminal baru tersebut jalankan perintah berikut untuk mengetes respon API di endpoint `/protected`.

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

Sekarang buat sebuah todo list sederhana dengan ID &quot;{{NICKNAME}}-1&quot;, dalam contoh saya menggunakan **rioastamal-1**.

Gunakan token yang didapat sebelumnya pada header `Authorization`.

```sh
JWT_TOKEN="SOME_LONG_JWT_TOKEN"
```

```sh
curl -s -D /dev/stderr -XPUT \
-H "Content-type: application/json" \
-H "Authorization: Bearer $JWT_TOKEN" \
http://localhost:8080/todos/rioastamal-1 -d '
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

```sh
curl -s -D /dev/stderr \
-H "Content-type: application/json" \
-H "Authorization: Bearer $JWT_TOKEN" \
http://localhost:8080/todos/rioastamal-1 | jq .
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

### Deploy Code ke AWS Lambda

Terdapat dua cara utama untuk mengupload code ke AWS Lambda. Pertama adalah langsung dari komputer lokal Anda atau dari S3 Bucket. Kita akan menggunakan cara yang disebut kedua. File yang diupload dalam format zip.

Pastikan Anda berada pada root directory dari project serverless-todo-express-api. Kita akan memaket code API yang ada dalam sebuah zip.

Jalankan perintah berikut untuk mengupload code ke S3 Bucket. Nama bucket saya adalah **serverless-workshop-202303-rioastamal**, sesuaikan milik Anda sendiri.

```sh
export APP_FUNCTION_BUCKET=serverless-workshop-202303-rioastamal
bash build.sh
```

Setelah selesai seharusnya terdapat sebuah file zip dengan nama **serverless-todo-api.zip** pada bucket.

1. Masuk pada halaman [Amazon S3](https://s3.console.aws.amazon.com/s3/get-started)
2. Pilih **Buckets** dari menu, pilih bucket yang telah dibuat.
3. Pilih file **serverless-todo-api.zip**
4. Pada tab **Properties** copy nilai yang ada pada **Object URL**

Berikutnya kita akan mengupload zip dari bucket tersebut ke fungsi Lambda.

1. Masuk ke console [AWS Lambda](https://console.aws.amazon.com/lambda/home)
2. Pilih **Functions** pilih fungsi yang telah dibuat
3. Pada tab **Code**, pilih **Upload from**, pilih **Amazon S3 location**
4. Pada **Amazon S3 link URL** isikan dari nilai dari Object URL yang dicopy sebelumnya

Berikutnya memasukkan nilai ke environment variable.

1. Masuk halaman fungsi Lambda yang telah dibuat
2. Pilih tab **Configuration**, pilih **Environment variables**, pilih **Edit**
3. Pilih **Add environment variable**, masukkan nilai sesuai milik Anda:
   - Key: `APP_TABLE_NAME`, Value: `serverless-todo-rioastamal`
   - Key: `APP_PARAMSTORE_JWT_SECRET_NAME`, Value: `/rioastamal/serverless-todo/development/jwt-secret`
   - Key: `APP_FROM_EMAIL_ADDR`, Value: `EMAIL.ANDA+sender@gmail.com`
4. Pilih **Save**

#### Mencoba API lewat API Gateway

Kita akan mencoba apakah API berjalan normal ketika dijalankan di AWS Lambda. 

1. Masuk pada halaman [API Gateway](https://console.aws.amazon.com/apigateway/home)
2. Pilih API yang telah dibuat, contoh milih saya **serverless-todo-gw-rioastamal**
3. Copy nilai URL yang ada pada **Invoke URL**

Kembali pada terminal di AWS Cloud9. Jalankan perintah berikut untuk mencoba API. Ganti URL dengan milik Anda sendiri.

```sh
curl -s -D /dev/stderr \
https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com/protected | jq .
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

Terakhir kali kita ubah nilainya menjadi `index.main` yang artinya Lambda akan coba mencari file `index.js` dan memanggil fungsi `main`. Sedangkan aplikasi kita sekarang menggunakan file `lambda.js` dan fungsi yang perlu dipanggil adalah `handler`.

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

Pada code diatas kita mengimpor object `app` dari file utama yaitu `index.js`. Aplikasi tidak melakukan bind ke port seperti pada lokal. Namun hanya mengekspor sebuah fungsi pada atribut `handler`.

Kita memanfaatkan library [serverless-http](https://www.npmjs.com/package/serverless-http) untuk mengubah perilaku dari request/response Lambda ke bentuk HTTP request normal yang dimengerti oleh express.

Sekarang pada terminal di AWS Cloud9 coba ulangi request yang gagal tadi. Harusnya sekarang sudah bisa. Ganti URL dengan milik Anda sendiri.

```sh
curl -s -D /dev/stderr \
https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com/protected | jq .
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
https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com/login \
 -d '
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
  "message": "Internal Server Error"
}
```

Ooops error apa lagi ini. Mari kita troubleshoot.

1. Masuk pada halaman fungsi Lambda yang dibuat
2. Pilih tab **Monitor**, pilih **Logs**
3. Pada **Recent invocations** pilih log paling baru pada kolom **LogStream**

Anda akan dibawa ke halaman Amazon CloudWatch. Pada **Log Events** pada salah satu baris harusnya terdapat error yang mirip seperti berikut.

```
{
    "errorType": "Runtime.UnhandledPromiseRejection",
    "errorMessage": "AccessDeniedException: User: arn:aws:sts::212473567997:assumed-role/serverless-todo-api-rioastamal-role-pkqnzkp3/serverless-todo-api-rioastamal is not authorized to perform: dynamodb:GetItem on resource: arn:aws:dynamodb:ap-southeast-1:212473567997:table/serverless-todo-development because no identity-based policy allows the dynamodb:GetItem action",
...
```

Dapat dilihat ternya kita memiliki masalah permission yaitu fungsi Lambda tidak memiliki permission untuk memanggil API **dynamodb:GetItem**. Kita akan memperbaiki masalah ini.

#### Menambahkan Permission ke Fungsi Lambda

Pada aplikasi Node.js yang dibuat tergantung pada beberapa layanan AWS yang lain seperti Amazon DynamoDB, Amazon SES, dan Amazon Systems Manager. Cara yang direkomendasikan untuk memberikan permission adalah dengan konsep [_least-privilege_](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege).

Artinya permission atau hak ases hanya diberikan seperlunya saja, cukup hanya untuk fungsi tersebut dapat berjalan. Namun pada workshop ini kita akan memberikan permission yang sedikit melebar untuk mempermudah pemahaman.

Langkah untuk menambahkan permission pada fungsi Lambda.

1. Masuk pada halaman fungsi Lambda yang dibuat.
2. Pilih tab **Configuration**, pilih **Permissions**
3. Pada bagian **Execution role** terdapat **Role name** yang digunakan oleh fungsi Lambda kita.
4. Pilih role tersebut, contohnya **serverless-todo-api-rioastamal-role-pkqnzkp3**

Anda akan dibawa ke halaman IAM untuk mengedit role. Pastikan Anda berada pada halaman **Summary** dari role ini.

1. Pada tab **Permissions** pilih **Add permissions**
2. Pilih **Attach policies**
3. Pada **Other permissions policies** ketik **dynamodb** lalu ENTER
4. Centang **AmazonDynamoDBFullAccess**, pilih **Clear filters**
5. Pada **Other permissions policies** ketik **ses** lalu ENTER
6. Centang **AmazonSESFullAccess**, pilih **Clear filters**
7. Pada **Other permissions policies** ketik **ssm** lalu ENTER
8. Centang **AmazonSQSFullAccess**, pilih **Clear filters**
9. Pilih **Add permissions**

Mari ulangi proses pemanggilan endpoint `/login` yang gagal sebelumnya.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com/login \
 -d '
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
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6IndvcmtzaG9wLXVzZXIxIiwiZW1haWwiOiJhc3RhbWFsLnJpbytyZWNlaXZlckBnbWFpbC5jb20iLCJleHAiOjE2Nzc2OTQ1ODQsImlhdCI6MTY3NzY1MTM4NH0.gvDbKwUrAhVQuoU4vcuhx0ke9iqiNJZHcH0VoAviCOw"
}
```

Proses berhasil dan mengembalikan JWT token. Sekarang mari coba registrasi pengguna baru. Pastikan email penerima adalah yang sudah di-verifikasi di Amazon SES sebelumnya.

```sh
curl -s -D /dev/stderr -H "Content-type: application/json" \
https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com/register -d '
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

### Fork Repository serverless-todo-vue di GitHub

Pada implementasi CI/CD frontend di AWS Amplify nantinya Anda akan menggunakan repository hasil fork dari serverless-todo-vue.

1. Pastikan Anda sudah login ke Akun GitHub Anda.
2. Buka repo [serverless-todo-vue](https://github.com/rioastamal-examples/serverless-todo-vue)
3. Pilih **Fork** di sisi kanan atas
4. Biarkan opsi lain sesuai nilai default, Pilih **Create fork**

{{GAMBAR_GITHUB_FORK}}

{{GAMBAR_GITHUB_CREATE_FORK_BUTTON}}

### Hosting Frontend di AWS Amplify

Aplikasi frontend dibuat menggunakan HTML/Javascript/Vue.js. Terdiri dari tiga file yaitu `index.html`, `login.htl`, dan `register.html`. Aplikasi dimodifikasi dari contoh [Vue.js TodoMVC](https://vuejs.org/examples/#todomvc).

Salah satu fitur utama AWS Amplify adalah hosting static website dan integrasi CI/CD secara otomatis dengan layanan version control seperti AWS CodeCommit atau GitHub.

Selain itu aplikasi akan ditempatkan di Content Delivery Network (CDN) terdekat dengan pengguna secara otomatis sehingga akses bisa lebih cepat.

Pada workshop ini kita akan menggunakan GitHub sebagai tempat menyimpan repository code frontend kita. Pastikan Anda memiliki akun GitHub untuk mengikuti proses ini.

#### Membuat App di Amplify Hosting

Pastikan Anda sudah melakukan fork repository serverless-vue-todo.

1. Masuk ke halaman [AWS Amplify](https://console.aws.amazon.com/amplify/home)
2. Pilih **GET STARTED**
3. Pada bagian Amplify Hosting pilih **Get started**
4. Pilih **GitHub**, pilih **Continue**

Amplify Hosting memerlukan akses read pada repository yang ingin diintegrasikan. Akan muncul dialog bahwa AWS Amplify memerlukan permission untuk mengakses repository.

1. Pilih **Authorize AWS Amplify (ap-southeast-1)**
2. (Jika repository tidak muncul) Pilih **View GitHub permissions**, pilih akun GitHub tempat repository berada, pilih **All repositories**, pilih **Install & Authorize**
3. Pada halaman Add repository branch, pada Recently updated repositories pilih repository **serverless-todo-vue**
4. Pada **Branch** pilih **main**, pilih **Next**

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

3. **Build image** biarkan kosong
4. Buka **Advanced settings** masukkan environment variable berikut.
    - Key: `API_BASE_URL`, Value: `https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com` (pastikan untuk mengganti sesuai alamat HTTP API Anda di API Gateway)
5. Pilih **Next**, pilih **Save and deploy**

Tunggu hingga proses build selesai ditandai dengan indikator _Provision_, _Build_, dan _Deploy_ berwarna hijau. Anda dapat memilih salah satu tersebut untuk melihat detil log dari masing-masing.

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

#### Mencoba Frontend Todo App

Jika proses build selesai, pilih main untuk melihat detil dari proses build terakhir. Kemudian pilih link URL pada **Domain** untuk membuka aplikasi frontend.

{{GAMBAR_HALAMAN_LOGIN}}

Aplikasi akan meredirect ke halaman `/login.html` jika pengguna belum melakukan otentikasi. Sebelumnya mencoba login aktifkan dulu debugger tools di web browser Anda dan buka tab Network.

{{GAMBAR_HALAMAN_LOGIN_ERROR}}

Hal yang harusnya terjadi adalah proses otentikasi gagal. Jika Anda membuka Debugger tools pada web browser maka pesan error yang muncul karena malasah CORS.

#### Mengatasi CORS via Amazon API Gateway

Ada dua cara untuk mengatasi masalah CORS ini, Anda mengubah source code dari API untuk menambahkan CORS HTTP header atau melalui Amazon API Gateway. Kita akan menggunakan cara kedua.

1. Buka halaman [Amazon API Gateway](https://console.aws.amazon.com/apigateway/main/)
2. Pilih API yang telah dibuat sebelumnya, milik saya adalah **serverless-todo-gw-rioastamal**
3. Pada menu **Develop** sisi kiri pilih **CORS**, pilih **Configure**
4. Pada masing-masing **Access-Control-Allow-Origin** dan **Access-Control-Allow-Headers** isikan &quot;\*&quot; lalu pilih **Add**
5. Pada **Access-Control-Allow-Methods** pilih **\***
6. Pilih **Save**

{{GAMBAR_KONFIGURASI_CORS}}

Kembali ke halaman `/login.html` dan coba ulangi kembali prosesnya. Harusnya sekarang Anda bisa login.

#### Mencoba Membuat Todo Baru

Sekarang Anda harusnya berada pada halaman `/index.html`. Jika belum maka buka halaman tersebut. Ketika anda pertama kali mengakses Todo list ini maka aplikasi akan secara otomatis mencoba membuat Todo kosong dengan ID acak.

{{GAMBAR_TODO_ID_RANDOM}}

Masih ingat pada langkah [Mencoba Endpoint PUT /todos/:id](#) Anda pernah membuat Todo via CLI di AWS Cloud9? Kita akan coba meng-_update_-nya. Pada contoh tersebut saya membuat todo dengan ID **rioastamal-1**.

1. Pada inputan **Create or update todos** masukkan **rioastamal-1** (ganti sesuai Todo ID yang Anda masukkan sebelumnya) tekan ENTER
2. Akan muncul dua todo item yaitu &quot;Workshop Serverless&quot; dan &quot;Pulang makan&quot;, isikan item todo baru &quot;Tidur&quot; tekan ENTER

{{GAMBAR_INPUT_TODO_ID_LAMA}}

{{GAMBAR_INPUT_TODO_TIDUR}}

Anda dapat masuk ke halaman [DynamoDB Table](https://console.aws.amazon.com/dynamodbv2/home/#table) yang dibuat sebelumnya untuk melihat data-data yang sudah dimasukkan.

### Menambahkan Link antar Halaman

Untuk mencoba implementasi CI/CD yang ada di Amplify Hosting kita akan mengubah sedikit tampilan dari halaman yang ada dengan menambahkan link di bagian bawah.

Pertama kita akan melakukan clone project serverless-todo-vue dari GitHub terlebih dulu.

1. Buka halaman project serverless-todo-vue yang telah anda fork di GitHub
2. Pilih **Code**, pada pilihan **Clone** di tab **Local** pastikan **SSH** terpilih
3. Copy repository URL versi SSH tersebut

{{GAMBAR_COPY_HTTPS_REPO}}

Masuk pada terminal di AWS Cloud9. Pastikan Anda berada pada direktori `~/environment`.

```sh
cd ~/environment
```

Gunakan perintah `git clone` berikut untuk meng-clone project. Sesuaikan dengan alamat repo Anda sendiri.

```sh
git clone git@github.com:rioastamal-examples/serverless-todo-vue.git
```

#### File login.html

Buka file `login.html` dan tambahkan link ke halaman `/register.html` dengan menghilangkan komentar sekitar baris 30.

```html
<!-- BEGIN - Remove this line
<section class="main">
  <p><a href="register.html">Register</a></p>
</section>
END - Remove this line -->
```

Menjadi seperti berikut.

```html
<section class="main">
  <p><a href="register.html">Register</a></p>
</section>
```

#### File register.html

Buka file `register.html` dan tambahkan link ke halaman `/login.html` dengan menghilangkan komentar sekitar baris 48.

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

Buka file `index.html` dan tambahkan link ke halaman `/login.html?logout` dengan menghilangkan komentar sekitar baris 288.

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
cd ~/environment/serverless-vue-todo
```

Lakukan `git commit` untuk menyimpan perubahan.

```sh
git add .
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

Hal ini menunjukkan AWS Amplify mendeteksi perubahan dan melakukan build secara otomatis. Dengan ini Anda tidak perlu mengelola CI/CD server sendiri untuk frontend.

Ketika build selesai coba kembali buka halaman aplikasi Frontend untuk memastikan bahwa perubahan telah ter-deploy dengan sempurna.

### Decoupling Proses Registrasi

Jika Anda perhatikan proses registrasi masih terikat dengan proses lain yang sebenarnya bisa dipisah yaitu proses pengiriman email. 

Bayangkan jika layanan email bermasalah maka proses proses registrasi dianggap gagal oleh pengguna akhir. Padahal proses pengiriman sebenarnya bisa menunggu beberapa saat setelah registrasi selesai.

Untuk melakukan decoupling bisa digunakan queue dan proses registrasi akan mengirim pesan ke queue. Selanjutnya pesan di queue akan diproses worker yang bertugas untuk mengirimkan email.

Worker untuk mengirim email nantinya akan dijalankan pada AWS Lambda dan Amazon SQS untuk menyimpan queue. 

Kita dapat mengintegrasikan Amazon SQS dengan AWS Lambda dengan mudah. Dimana jika terdapat pesan baru masuk ke queue di SQS, maka pesan tersebut dapat diteruskan ke sebuah fungsi Lambda.

{{GAMBAR_ARSITEKTUR_BARU}}

### Membuat SQS queue

Kita akan menggunakan Amazon SQS untuk melakukan decoupling API dari task yang tidak harus selesai saat itu juga. Dalam hal ini task pengiriman welcome email. 

Task ini tidak harus langsung selesai ketika API registration dipanggil dan ada kemungkinan proses pengiriman email lambat. Sehingga task ini kandidat yang cocok untuk dikirim ke queue.

1. Masuk pada halaman [Amazon SQS](https://console.aws.amazon.com/sqs/home) 
2. Pilih **Create queue**
2. Pada pilihan **Type** pilih **Standard** dan pada **Name** isikan &quot;serverless-todo-welcome-email-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-welcome-email-rioastamal**
3. Sisanya biarkan sesuai nilai bawaan, kemudian pilih **Create queue**

Nantinya queue ini akan digunakan untuk menyimpan pesan yang dikirimkan oleh API ketika user baru saja mendaftar.

Kemudian pesan ini akan diproses oleh worker dalam hal ini adalah sebuah fungsi Lambda.

### Membuat Welcome Email Worker dengan AWS Lambda

Fungsi Lambda ini bertugas untuk memproses pesan yang ada di queue yang dikirim oleh proses registrasi.

1. Masuk pada halaman [AWS Lambda](https://console.aws.amazon.com/lambda/home). kemudian halaman **Functions**, pilih **Create function**
2. Pada **Function name** isikan &quot;serverless-todo-email-worker-{{NICKNAME}}&quot;, contoh milik saya **serverless-todo-email-worker-rioastamal**
3. Pada **Runtime** pilih **Node.js 16.x** kemudian **Architecture** pilih **x86_64**
4. Pada **Change default execution role**, pilih **Use an existing role**, pada **Existing role** pilih role yang sebelumnya dibuat, contoh milik saya **service-role/serverles-todo-api-rioastamal-role-RANDOM**
5. Sisanya biarkan sesuai nilai bawaan, kemudian pilih **Create function**

Kita memilih role yang sebelumnya agar kita tidak perlu melakukan setup ulang permission. Pada kasus production Anda harusnya hanya memberikan permission yang diperlukan saja.

Selanjutnya kita akan mengkonfigurasi environment variable yang diperlukan.

1. Pada fungsi Lambda yang dibuat, pilih tab **Configuration**, pilih **Environment variables**
2. Pilih **Edit**, pilih **Add environment variable**, tambahkan environment variable berikut tapi sesuaikan dengan milik Anda.
   - **Key**: `APP_TABLE_NAME`, **Value**: `serverless-todo-rioastamal`
   - **Key**: `APP_URL`, **Value**: `https://main.d1f7gufsd46hhz.amplifyapp.com` (URL dari Amplify Hosting untuk aplikasi frontend)
   - **Key**: `APP_FROM_EMAIL_ADDR`, **Value**: `EMAIL.ANDA+sender@gmail.com` (Ganti dengan email pengirim yang telah diverifikasi di Amazon SES)
3. Pilih **Save**

Fungsi Lambda ini perlu untuk mengakses Amazon SQS jadi permission perlu ditambahkan pada _execution role_.

1. Masih pada tab **Configuration**, pilih **Permissions**
2. Pada **Execution Role** pilih **Role name** untuk membuka halaman IAM
3. Pilih **Add Permissions**, pilih **Attach policies**
4. Pada pencarian isikan &quot;SQS&quot; kemudian ENTER
5. Centang **AWSLambdaSQSQueueExecutionRole** 
6. Pilih **Add permissions**

Langkah berikutnya adalah integrasi SQS queue dengan fungsi Lambda.

1. Masih pada tab **Configuration**, pilih **Triggers*, pilih **Add trigger**
2. Pada **Trigger configuration** ketik &quot;SQS&quot; lalu pilih **SQS**
3. Pada **SQS queue** pilih queue yang telah dibuat sebelumnya, milik saya adalah **serverless-todo-welcome-email-rioastamal**
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

Berbeda dengan code di `serverless-todo-api` di pada worker ini 100% dibuat khusus untuk dijalankan di Lambda sehingga tidak ada proses binding port dan sebagainya. Dapat dilihat pada `src/handlers/main.js`.

Untuk menjalankan di lokal Anda perlu memanggil file `local.js`. File ini akan membaca file `event-sqs.sample.json` yang mensimulasikan event dari SQS.

Kita akan mencoba mengirim ulang email registrasi ke username `workshop-user1`. Ada beberapa environment variable yang perlu diset yaitu.

- `APP_TABLE_NAME` nama tabel di DynamoDB
- `APP_FROM_EMAIL_ADDR` email pengirim yang verified di Amazon SES
- `APP_URL` URL frontend di Amplify Hosting
- `APP_DUMMY_SQS_BODY` simulasi data yang dikirim (hanya untuk eksekusi lokal saja)

Sesuaikan nilainya dengan milik Anda.

```sh
export APP_TABLE_NAME=serverless-todo-rioastamal \
APP_URL=https://qb63qt4402.execute-api.ap-southeast-1.amazonaws.com \
APP_FROM_EMAIL_ADDR=EMAIL.ANDA+sender@gmail.com \
APP_DUMMY_SQS_BODY='{ "username": "workshop-user1" }'
```

```sh
node local.js
```

Jika sukses maka harusnya anda menerima email. Cek inbox email yang digunakan oleh `workshop-user1`.

#### Deploy Email Worker ke Fungsi Lambda

Script akan kita paket menjadi zip dan upload ke S3 bucket, kemudian kita impor ke fungsi AWS Lambda yang dibuat.

Pastikan masih berada pada root directory `serverless-todo-email-worker`. Jalankan script `build.sh` dan set nilai dari environment variable `APP_FUNCTION_BUCKET` sesuai dengan milik Anda.

```sh
APP_FUNCTION_BUCKET=serverless-workshop-202303-rioastamal \
bash build.sh
```

Perlu diingat fungsi utama dari worker email adalah file `src/handlers/main.js` dan nama fungsinya `welcomeEmailSender`. Sehingga Anda perlu mengubah konfigurasi Handler.

1. Masuk pada halaman fungsi Lambda serverless-todo-email-worker
2. Pada bagian **Runtime settings** pilih **Edit**
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

### Decoupling Email dari Backend

Proses decoupling pada sisi backend di project `serverless-todo-api` adalah membuang code proses pengiriman email dan menggantinya dengan queue.

Pada workshop ini saya sudah menyiapkan code yang sudah jadi di branch yang berbeda yaitu `decoupled-welcome-email`. 

Pada terminal di AWS Cloud9 gunakan perintah berikut untuk melihat remote branch di project `serverless-todo-api`.

```sh
cd ~/environment/serverless-todo-api
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

Sekarang harusnya ada dua branch di local.

```sh
git branch
```

```
* decoupled-welcome-email
  main
```

Coba buka kembali file `src/index.js` maka akan ada perbedaan dengan sebelumnya. Dimana sekarang fungsi `sendWelcomeEmail` bukan mengirim email langsung melainkan hanya mengirim pesan ke SQS queue.

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

```sh
APP_FUNCTION_BUCKET=serverless-workshop-202303-rioastamal \
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
   - **Key**: `APP_SQS_URL`, **Value**: `https://sqs.ap-southeast-1.amazonaws.com/212473567997/serverless-todo-welcome-email-rioastamal` (Ganti dengan milik Anda)
4. Pilih **Save**

Sekarang harusnya proses decoupling pengiriman welcome email dan registrasi selesai.

Buka kembali halaman frontend, navigasi ke `/register.html` untuk mencoba melakukan registrasi user dengan backend yang sudah decoupling dari welcome email.

### Penutup

Selamat Anda telah menyelesaikan workshop &quot;Membangun Backend dan Frontend dengan Arsitektur Serverless&quot;. 

Pada workshop ini Anda telah mempelajari bagaimana memanfaatkan layanan-layanan AWS untuk deployment aplikasi backend dan frontend. AWS Lambda untuk menjalankan code, Amazon DynamoDB sebagai NoSQL database, AWS Amplify untuk hosting frontend, Amazon SES untuk mengirim email, Amazon API Gateway sebagai proxy/router, Amazon SQS untuk menyimpan queue dan AWS Systems Manager untuk menyimpan parameter bersifat secret.

Dengan serverless Anda dapat tidak perlu memikirkan tentang pengelolaan server. Serverless memiliki karakteristik auto-scaling, memiliki high availibility, dan scale-to-zero billing. 

Sehingga memungkinkan Anda untuk fokus ke aplikasi dan memungkinkan untuk rilis dan mendapatkan feedback lebih cepat.

### Pembersihan

Jika Anda menjalankan workshop ini menggunakan akun Anda sendiri maka Anda perlu menghapus resource yang telah dibuat untuk menghindari adanya tagihan biaya.

Anda dapat masuk ke masing-masing halaman layanan untuk menghapusnya. Contohnya masuk Amazon S3 dan menghapus file-file yang ada pada bucket yang digunakan pada workshop ini.