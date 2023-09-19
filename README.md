# Rifat Fauzan
# PBP B - 2206082184

## Link Aplikasi
Berikut merupakan link yang menuju kepada aplikasi saya: [Mon's Inventory Manager](https://monsfirstproject.adaptable.app/main/)

# Jawaban Soal Tugas 3

### Jelaskan bagaimana cara kamu mengimplementasikan *checklist* di atas secara *step-by-step* (bukan hanya sekadar mengikuti tutorial)
1. Membuat input `form` untuk menambahkan objek model pada app sebelumnya.
    - Buat file `forms.py` di folder "main" untuk membuat class `ItemForm` yang merupakan `ModelForm` untuk model `Item` untuk mendefinisikan atribut
    - Buat fungsi `create_item` dalam `views.py` yang mengelola input form. Dalam fungsi ini, form `ItemForm` diisi dengan data dari `request.POST` dan dilakukan pengecekan. Jika valid, data disimpan dan pengguna diarahkan kembali ke halaman utama
    - Menambahkan fungsi untuk merender `create_item.html`
    - Tambahkan `items = Item.objects.all()` pada fungsi `show_main` dalam `views.py` untuk mengambil semua objek `Item` dari database dan melakukan return sesuai konteks pada `views.py`
    - Import fungsi yang diperlukan dan tambahkan path URL untuk mengakses fungsi-fungsi ini dalam urls.py di `main`
    - Buat file `create_item.html` dalam folder "templates" di `main`
    - Tambahkan kode di `main.html` untuk menampilkan data item dalam bentuk tabel dan menambahkan tombol "Add New Item" yang mengarahkan ke halaman pengisian item yang ingin ditambahkan
2. Menambahkan Fungsi dalam `views.py` untuk mendapatkan data dalam bentuk JSON dan XML.
    - Buat fungsi `show_xml` dalam `views.py` dan menambahkan path URL. Fungsi ini mengambil data `Item` dan mengembalikannya dalam format XML
    - Buat fungsi `show_json` dalam `views.py` dan menambahkan path URL. Fungsi ini mengambil data `Item` dan mengembalikannya dalam format JSON
    - Buat fungsi `how_xml_by_id` dalam `views.py` dan menambahkan path URL. Fungsi ini mengambil data `Item` berdasarkan `ID` yang disediakan dan mengembalikannya dalam format XML
    - Buat fungsi `show_json_by_id` dalam `views.py` dan menambahkan path URL. Fungsi ini mengambil data `Item` berdasarkan `ID` yang disediakan dan mengembalikannya dalam format JSON

### Apa perbedaan antara form `POST` dan form `GET` dalam Django?
- **POST**
    POST melakukan pengiriman data melalui HTTP. POST lebih aman apabila digunakan untuk mengirimkan data yang sensitif seperti kata sandi dan sebagainya. POST juga digunakan untuk membuat, memperbarui, atau menghapus data di server. POST juga memiliki kemungkinan untuk mengirimkan data dengan ukuran yang tidak terhingga.
- **GET**
    GET melakukan pengiriman data melaui URL. GET juga berfungsi untuk mengambil data dari server serta memiliki batasan URL sebanyak 2047 karakter. Apabila melebihi batas, maka GET tidak dapat digunakan.

### Apa perbedaan utama antara XML, JSON, dan HTML dalam konteks pengiriman data?
- **XML**
    Dalam pengiriman data, XML menggunakan format dengan `<data>`, contohnya seperti `<nama>Rifat</nama>` sehingga lebih kompleks dan sulit dibaca. Namun, XML mendukung semua tipe data JSON dan data lain seperti Boolean, tanggal dan sebagainya.
- **JSON**
    Dalam pengiriman data, JSON menggunakan format yang lebih dibaca, contohnya seperti `{nama}:"Rifat"` sehingga tidak terlalu kompleks dan lebih fleksibel. Namun, JSON tidak mendukung jumlah tipe data yang lebih banyak daripada XML.
- **HTML**
    Dalam pengiriman data, HTML berfungsi untuk mengatur tampilan dalam sebuah web serta memiliki struktur yang mudah dibaca juga. Fungsi utama dari HTML yaitu untuk mengatur tampilan dan interaksi antar pengguna.

### Mengapa JSON sering digunakan dalam pertukaran data antara aplikasi web modern?
JSON lebih sering digunakan dalam pertukaran data antara aplikasi web modern karena JSON memiliki format yang lebih sederhana, ringkas, serta mudah dibaca oleh manusia ataupun oleh komputer. Dengan demikian, pemrosesan data dapat dilakukan dengan lebih cepat dalam melakukan pertukaran data untuk memaksimalkan pengalaman pengguna.

### Mengakses kelima URL di poin 2 menggunakan Postman, membuat screenshot dari hasil akses URL pada Postman, dan menambahkannya ke dalam `README.md`.
<img src='/Assets/Tugas3/SSHTML.jpg'>
<img src='/Assets/Tugas3/SSXML.jpg'>
<img src='/Assets/Tugas3/SSXMLBYID.jpg'>
<img src='/Assets/Tugas3/SSJSON.jpg'>
<img src='/Assets/Tugas3/SSJSONBYID.jpg'>


## Jawaban Tugas 2

### 1. Jelaskan bagaimana cara kamu mengimplementasikan checklist di atas secara step-by-step (bukan hanya sekadar mengikuti tutorial)
1. Pertama, saya membuat project baru Django dengan menggunakan virtual environment
    - Untuk mengaktifkan virtual environment pada Django, dapat dilakukan dengan menjalankan command tersebut pada terminal
    ```
    python -m venv env
    ```
    - Lalu mengaktifkan virtual environment dngan cara menjalankan command tersebut
    ```
    env\Scripts\activate
    ```
    - Pada directory yang sama, saya membuat file `requirements.txt` yang berisi dependencies yang perlu di install seperti Django dan sebagainya
    - Lalu saya menjalankan command `pip install -r requirements.txt` untuk menginstall dependencies tersebut
    - Kemudian untuk membuat project baru, saya menjalankan `django-admin startproject FirstProject .`
    - Untuk keperluan deployment, saya menambahkan `"*"` pada bagian `ALLOWED_HOSTS` di file `settings.py`
    - Saya juga menambahkan `.gitignore` agar GitHub mengabaikan file yang tidak perlu nantinya
2. Membuat aplikasi bernama `main`.
    - Menjalankan command `python manage.py startapp main` untuk membuat aplikasi bernama `main`
    - Membuat folder baru bernama `templates` di dalam folder `main` serta menambahkan `main.html` ke dalam folder tersebut. Nantinya file html tersebut berfungsi sebagai tampilan saat kita mengunjungi aplikasi tersebut
3. Membuat konfigurasi URL untuk mengakses aplikasi `main`.
    - Membuat file baru bernama `urls.py` di dalam folder `main` kemudian menambahkan `path('', show_main, name='show_main'),`
4. Pada berkas `urls.py` dalam folder FirstProject, menambahkan `path('main/', include('main.urls'))` pada bagian `url_patterns`. Nantinya ini berfungsi untuk mengatur URL tingkat proyek
5. Pada file `models.py` yang berada pada directory `main`, saya menambahkan atribut yang diperlukan seperti:
    - `name` dengan tipe Charfield
    - `date_added` dengan tipe DateField
    - `amount` dengan tipe IntegerField
    - `description` dengan tipe TextField
6. Pada file `views.py`, saya membuat function yang berfungsi untuk mereturn nama dan kelas di HTML-nya nanti
7. Melakukan deploy pada Adaptable
    - Melakukan add, push, serta commit ke repository GitHub
    - Melakukan deployment dari website adaptable dengan ketentuan seperti Tutorial 0

### 2. Buatlah bagan yang berisi request client ke web aplikasi berbasis Django beserta responnya dan jelaskan pada bagan tersebut kaitan antara `urls.py`, `views.py`, `models.py`, dan berkas `html`.
<img src="/Assets/Tugas2//flowchart.jpg">

### 3. Jelaskan mengapa kita menggunakan virtual environment? Apakah kita tetap dapat membuat aplikasi web berbasis Django tanpa menggunakan virtual environment?
Dengan menggunakan virtual environment pada project Django kita, maka kita membuat sebuah lingkungan virtual yang tidak terisolasi. Terisolasi yang dimaksud yaitu program yang berjalan dengan modul-modul yang diinstall dn program tersebut tidak dapat diakses dari luar. Dengan menggunakan virtual environment,maka proyek yang kita kerjakan akan terpisah dari dependencies proyek lain, sehingga tidak bertabrakan dengan dependencies proyek lainnya.

Kita tetap dapat membuat aplikasi berbasis web Django tanpa virtual environment. Namun, apabila kita tidak menggunakan virtual environment, maka bisa saja projek tersebut tidak terisolasi dan dependecies yang dipakai pada proyek tersebut bertabrakan dengan dependencies dari proyek lainnya.

### 4. Jelaskan apakah itu MVC, MVT, MVVM dan perbedaan dari ketiganya.
- `MVC` atau `Model-View-Controller` merupakan arsitektur yang membagi tiga komponennya menjadi model, view, dan controller.    
- `MVT` atau `Model-View-Template` merupakan arsitektur yang memisahkan komponen utama dari sebuah aplikasi.
- `MVVM` atau `Model-View-ViewModel` merupakan arsitektur yang memisahkan antara logika dan model melalui ViewModel.

Perbedaan dari ketiga arsitektur tersebut yaitu MVC lebih terfokus pada pengendalian alur kerja aplikasi, MVT menggunakan sebuah template untuk menggabungkan data dan tampilan, dan MVVM lebih fokus terhadap pemisahan antara tampilan, logika, dan data.

        