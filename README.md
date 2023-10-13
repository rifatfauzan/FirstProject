# Rifat Fauzan
# PBP B - 2206082184

## Link Aplikasi
Berikut merupakan link yang menuju kepada aplikasi saya: [Mon's Inventory Manager](https://monsfirstproject.adaptable.app/main/)

## Jawaban Soal Tugas 6
### Jelaskan bagaimana cara kamu mengimplementasikan checklist di atas secara step-by-step (bukan hanya sekadar mengikuti tutorial).
#### Mengubah tugas 5 yang telah dibuat sebelumnya menjadi menggunakan AJAX.
1. AJAX GET
    1. Ubahlah kode *cards* data item agar dapat mendukung AJAX GET.
        - Hapus kode *table* yang sudah dibuat sebelumnya
        - Tambahkan kode dibawah pada `main.html`
            ```
            <div class="table-responsive">
                <table class="table table-striped table-bordered">
                    <tbody id="item_table">
                    </tbody>
                </table>
            </div>
            ```
    2. Lakukan pengambilan task menggunakan AJAX GET.
        - Buat fungsi `get_item_json` pada `views.py` untuk Mengembalikan Data JSON
        - Menambahkan `<script>` pada `main.html`
            ```
            async function getItems() {
                return fetch("{% url 'main:get_item_json' %}").then((res) => res.json())
            }
            ```
2. AJAX POST
    1. Buatlah sebuah tombol yang membuka sebuah modal dengan form untuk menambahkan item.
        - Menambagkan kode dibawah pada `main.html`
            ```
            <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h1 class="modal-title fs-5" id="exampleModalLabel">Add New Item</h1>
                            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                        </div>
                        <div class="modal-body">
                            <form id="form" onsubmit="return false;">
                                {% csrf_token %}
                                <div class="mb-3">
                                    <label for="name" class="col-form-label">Name:</label>
                                    <input type="text" class="form-control" id="name" name="name"></input>
                                </div>
                                <div class="mb-3">
                                    <label for="amount" class="col-form-label">Amount:</label>
                                    <input type="number" class="form-control" id="amount" name="amount"></input>
                                </div>
                                <div class="mb-3">
                                    <label for="description" class="col-form-label">Description:</label>
                                    <textarea class="form-control" id="description" name="description"></textarea>
                                </div>
                            </form>
                        </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                            <button type="button" class="btn btn-primary" id="button_add" data-bs-dismiss="modal">Add Item</button>
                        </div>
                    </div>
                </div>
            </div>
            ```
        - Membuat button untuk menambahkan item
            ```
            <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">Add Item by AJAX</button>

            ```
    2. Buatlah fungsi view baru untuk menambahkan item baru ke dalam basis data.
        - Membuat Fungsi `add_item_ajax` pada `views.py`
            ```
            @csrf_exempt
            def add_item_ajax(request):
                if request.method == 'POST':
                    name = request.POST.get("name")
                    amount = request.POST.get("amount")
                    description = request.POST.get("description")
                    user = request.user

                    new_item = Item(name=name, amount=amount, description=description, user=user)
                    new_item.save()

                    return HttpResponse(b"CREATED", status=201)

                return HttpResponseNotFound()
            ```
    3. Buatlah path `/create-ajax/` yang mengarah ke fungsi *view* yang baru kamu buat.
        - Menambahkan Routing Untuk Fungsi `add_item_ajax` pada `urls.py`
        - Menambahkan Routing Untuk Fungsi `get_item_json` pada `urls.py`
    4. Hubungkan form yang telah kamu buat di dalam modal kamu ke *path* `/create-ajax/`.
        - Tambahkan fungsi `addItem` pada `<script>` di `main.html`
            ```
            function addItem() {
                fetch("{% url 'main:add_item_ajax' %}", {
                    method: "POST",
                    body: new FormData(document.querySelector('#form'))
                }).then(refreshItems)

                document.getElementById("form").reset()
                return false
            }
            ```
    5. Lakukan *refresh* pada halaman utama secara asinkronus untuk menampilkan daftar item terbaru tanpa *reload* halaman utama secara keseluruhan.
        - Tambahkan fungsi `refreshItems` pada `<script>` di `main.html`
            ```
            async function refreshItems() {
                document.getElementById("item_table").innerHTML = ""
                const items = await getItems()
                let htmlString = `<tr>
                    <th>Name</th>
                    <th>Amount</th>
                    <th>Description</th>
                    <th>Date Added</th>
                    <th>Action</th>
                </tr>`
                items.forEach((item) => {
                    htmlString += `\n<tr>
                    <td>${item.fields.name}</td>
                    <td>${item.fields.amount}</td>
                    <td>${item.fields.description}</td>
                    <td>${item.fields.date_added}</td>
                    <td>
                        <button class="btn btn-danger" data-url="{% url 'main:delete_item_ajax' 123 %}" onclick="deleteItem(this, ${item.pk})">Delete</button>
                    </td>
                </tr>` 
                })
                
                document.getElementById("item_table").innerHTML = htmlString
            }
            ```
### Jelaskan perbedaan antara Asynchronous Programming dengan Synchronous Programming.
1. **Synchronous Programming**
    - **Eksekusi Berurutan**: Dalam synchronous programming, tugas-tugas dieksekusi secara berurutan. Setiap tugas harus menunggu tugas sebelumnya selesai sebelum dapat dimulai.
    - **Thread Single**: Biasanya hanya menggunakan satu thread atau proses. Ketika thread utama terblokir oleh tugas yang lambat, aplikasi bisa terasa tidak responsif.
    - **Prediktabilitas**: Kode synchronous lebih mudah dipahami dan diprediksi karena eksekusi berlangsung dalam urutan yang jelas.
    - **Sederhana**: Kode synchronous sering lebih mudah ditulis karena Anda tidak perlu mengkhawatirkan koordinasi antara tugas-tugas paralel.
2. **Asynchronous Programming**
    - **Non-Blocking Execution**: Dalam asynchronous programming, tugas-tugas dapat dieksekusi secara bersamaan tanpa harus menunggu satu sama lain. Tugas yang lambat atau terblokir tidak akan menghentikan tugas lainnya.
    - **Multi-Threaded atau Event-Driven**: Melibatkan penggunaan multiple threads atau event-driven programming, memungkinkan aplikasi tetap responsif bahkan saat ada tugas yang memakan waktu.
    - **Kompleksitas**: Lebih kompleks karena Anda perlu mengelola bagaimana tugas-tugas berinteraksi dan menyusun aliran eksekusi yang benar.
    - **Efisiensi**: Lebih efisien dalam situasi di mana banyak tugas harus dijalankan secara bersamaan, seperti dalam aplikasi web yang melayani banyak permintaan HTTP secara bersamaan.
    - **Responsif**: Aplikasi akan tetap responsif terhadap input pengguna, bahkan saat ada tugas yang memakan waktu di latar belakang.

### Dalam penerapan JavaScript dan AJAX, terdapat penerapan paradigma event-driven programming. Jelaskan maksud dari paradigma tersebut dan sebutkan salah satu contoh penerapannya pada tugas ini.
Paradigma event-driven programming adalah pendekatan pemrograman yang berfokus pada penggunaan peristiwa (events) sebagai pusat kendali eksekusi program. Dalam paradigma ini, program tidak hanya dieksekusi secara linier dari atas ke bawah, tetapi merespons peristiwa-peristiwa yang terjadi. Peristiwa dapat berasal dari berbagai sumber, seperti input pengguna (klik mouse, keyboard, dan sebagainya), data yang diterima dari server, atau peristiwa-peristiwa lainnya yang terjadi di dalam program itu sendiri. Saat suatu peristiwa terjadi, program akan menjalankan kode yang telah ditentukan untuk menangani peristiwa tersebut.

Contoh pengaplikasiannya pada tugas ini yaitu dengan adanya fitur-fitur seperti `addItem()` serta `deleteItem()`, yang ketika ditekan tombolnya, maka akan mengeksekusi suatu perintah berdasarkan isi dari scriptnya tersebut.

### Jelaskan penerapan asynchronous programming pada AJAX.
Asynchronous programming dalam konteks AJAX merujuk pada kemampuan untuk menjalankan operasi jaringan (seperti permintaan HTTP) tanpa harus menghentikan eksekusi kode utama program. Hal ini memungkinkan aplikasi web untuk tetap responsif terhadap input pengguna dan untuk menjalankan tugas-tugas lainnya tanpa harus menunggu respon dari server.

### Pada PBP kali ini, penerapan AJAX dilakukan dengan menggunakan Fetch API daripada library jQuery. Bandingkanlah kedua teknologi tersebut dan tuliskan pendapat kamu teknologi manakah yang lebih baik untuk digunakan.
1. **Fetch API**
    - **Kelebihan**: Standar web, lebih ringan, menggunakan Promises, waktu pemuatan lebih cepat.
    - **Kelemahan**: Belajar kurva, kode lebih panjang.
2. **jQuery**
    - **Kelebihan**: Kompatibel dengan berbagai browser, sederhana, banyak plugin.
    - **Kelemahan**: Tambahan berat, lebih lambat daripada Fetch API

Menurut pendapat saya, Fetch API lebih baik digunakan dibandingkan dengan jQuery karena Fetch API memiliki efisiensi yang lebih baik, performa yang lebih cepat, dan ringan dalam penggunaannya. 


# Jawaban Soal Tugas 5
### Jelaskan bagaimana cara kamu mengimplementasikan *checklist* di atas secara *step-by-step* (bukan hanya sekadar mengikuti tutorial)
- `base.html`
    - Pada `base.html`, saya menambahkankan navbar pada bagian atas yang berisi tombol `home` untuk kembali ke tampilan utama situs.
- `main.html`
    - Pada `main.html`, saya menambahkan class `card` untuk membuat informasi user dalam bentuk model `card`.
    - Memberikan margin dan padding agar lebih ke tengah.
    - Menambahkan warna pada tombol sesuai dengan fungsinya.
- `login.html`
    - Pada `login.html`, saya membuat class baru bernama `.login-card` yang berfungsi untuk mengatur posisi, warna, drop shadow, dan sebagainya dalam bentuk card.
    - Menambahkan `login-card .login_btn:hover` yang berfungsi apabila cursor di-hover di atas button, maka akan berubah warna.
- `register.html`
    - Pada `register.html`, saya membuat class baru bernama `.register-card` yang berfungsi untuk mengatur posisi, warna, drop shadow, dan sebagainya dalam bentuk card.
    - Menambahkan `register-card .register_btn:hover` yang berfungsi apabila cursor di-hover di atas button, maka akan berubah warna.
- `create_item.html`
    - Pada `create_item.html`, saya membuat class baru bernama `.card` yang berfungsi untuk mengatur posisi, warna, drop shadow, dan sebagainya dalam bentuk card.
    - Menambahkan `register-card .register_btn:hover` yang berfungsi apabila cursor di-hover di atas button, maka akan berubah warna.

### Jelaskan manfaat dari setiap element selector dan kapan waktu yang tepat untuk menggunakannya.

1. **Selector Elemen**: Selector ini digunakan untuk memilih elemen di dalam elemen dengan jenis tertentu. Ini memungkinkan pengguna untuk menerapkan gaya atau aturan tertentu pada semua elemen dengan jenis yang sama di situs web. Sebagai contoh, kita dapat menggunakan kode seperti berikut untuk mengubah gaya semua elemen `<p>` atau paragraf:
   ```css
   p {
     font-size: 16px;
     line-height: 1.5;
     color: #333;
   }
   ```
   Selector ini akan memengaruhi semua elemen paragraf dalam halaman.

2. **Selector Kelas**: Selector ini digunakan untuk memilih satu atau lebih elemen berdasarkan nama kelas yang diberikan dalam atribut `class`. Ini berguna saat kita ingin menerapkan gaya atau aturan pada elemen-elemen yang memiliki kelas tertentu sehingga gaya tersebut dapat digunakan berulang kali saat mengakses elemen dengan kelas yang sama. Sebagai contoh, kita dapat menggunakan kode berikut untuk mengubah gaya semua elemen dengan kelas `button`:
   ```css
   .button {
     background-color: #3498db;
     color: #fff;
     padding: 10px 15px;
   }
   ```

3. **Selector ID**: Selector ini digunakan untuk memilih elemen secara individual berdasarkan nilai atribut `id` yang spesifik. Ini berguna ketika kita ingin memberikan gaya yang unik pada elemen tertentu sehingga hanya elemen dengan ID yang cocok yang akan memengaruhi gaya tersebut. Sebagai contoh, kita dapat menggunakan kode berikut untuk mengubah gaya elemen dengan ID `header`:
   ```css
   #header {
     font-size: 24px;
     color: #2c3e50;
     text-align: center;
   }
   ```

### Penjelasan Mengenai Tag HTML5 yang Dikenal

- `<html>`  : Menandakan awal dan akhir dari dokumen HTML.
- `<title>` : Mengatur judul dokumen HTML.
- `<head>`  : Memberikan informasi tambahan tentang dokumen HTML.
- `<h1 - h6>`: Digunakan untuk membuat judul dengan ukuran berbeda pada dokumen HTML.
- `<p>`     : Menunjukkan awal dari paragraf pada HTML.
- `<button>`: Membuat tombol yang dapat diklik pada HTML.
- `<br>`    : Menghasilkan jeda baris untuk memberi ruang kosong di suatu baris.
- `<table>` : Membuat tabel dengan baris dan kolom pada HTML.
- `<div>`   : Menandai sebuah bagian atau divisi dalam HTML.

### Perbedaan Antara Margin dan Padding

Margin adalah ruang di sekitar elemen HTML yang memisahkan elemen dari elemen lain di sekitarnya. Ini digunakan untuk mengatur jarak antara elemen dan elemen lainnya, dan margin menambahkan ruang di luar elemen, sehingga memperbesar ukuran total elemen tersebut. Margin memengaruhi penempatan elemen terhadap elemen lain di sekitarnya, dan bisa memiliki nilai negatif untuk mengarahkan elemen lebih dekat satu sama lain.

Di sisi lain, Padding adalah ruang di sekitar konten dan elemen HTML, yang berada di antara konten dan batas elemen tersebut. Ini digunakan untuk menambahkan ruang internal ke dalam elemen, tetapi ukuran elemen itu sendiri tetap sama. Padding memengaruhi penempatan konten dalam elemen, dan tidak bisa memiliki nilai negatif.

### Perbedaan Antara Framework CSS Tailwind dan Bootstrap

Tailwind adalah kerangka kerja yang menerapkan pendekatan "utility-first," di mana pengguna membangun desain dengan menggabungkan kelas utilitas ke dalam elemen HTML. Ini menyediakan fleksibilitas dan kemampuan kustomisasi yang tinggi, tetapi juga memerlukan pemahaman yang lebih dalam. Bootstrap, di sisi lain, menyediakan sejumlah besar kelas CSS dan komponen yang telah dirancang sebelumnya. Ini memberikan stabilitas dan kemudahan penggunaan, tetapi dapat memiliki keterbatasan dalam fleksibilitas desain yang unik.

Sebaiknya menggunakan Bootstrap ketika Anda ingin membuat situs web sederhana yang tidak memerlukan banyak kustomisasi dalam CSS. Tailwind cocok digunakan ketika Anda perlu mengkustomisasi tampilan dengan tingkat yang lebih tinggi atau saat Anda ingin memiliki kendali yang lebih besar atas desain elemen-elemen Anda.

# Jawaban Soal Tugas 4

### Jelaskan bagaimana cara kamu mengimplementasikan *checklist* di atas secara *step-by-step* (bukan hanya sekadar mengikuti tutorial)
1. Mengimplementasikan fungsi registrasi, login, dan logout untuk memungkinkan pengguna untuk mengakses aplikasi sebelumnya dengan lancar.
    - **REGISTER**
        - Pada file  `views.py` melakukan import `redirect`, `UserCreationForm`, `messages`
        - Membuat fungsi `register` pada `views.py` untuk form registrasi dan menghasilkan akun pengguna ketika data di-*submit* dari form
        - Membuat file baru pada `main/templates` dengan nama `register.html` untuk membuat *template* dari *register*
        - Melakukan import fungsi `register` pada direktori `main/urls.py`  
        - Menambahkan *path URL* pada `urls.py`
    - **LOGIN**
        - Pada file  `views.py` melakukan import `authenticate` dan `login`
        - Membuat fungsi `login_user` pada `views.py` untuk mengautentikasi user yang ingin melakukan *login*
        - Membuat file baru pada `main/templates` dengan nama `login.html` untuk membuat *template* dari *login*
        - Melakukan import fungsi `login_user` pada direktori `main/urls.py`  
        - Menambahkan *path URL* pada `urls.py`
    - **LOGOUT**
        - Pada file  `views.py` melakukan import `logout`
        - Membuat fungsi `logout_user` pada `views.py` untuk mengautentikasi user yang ini melakukan *logout*
        - Menambahkan potongan kode pada `main/templates/main.html` untuk button logout
        - Melakukan import fungsi `logout_user` pada direktori `main/urls.py`  
        - Menambahkan *path URL* pada `urls.py`

2. Membuat **dua** akun pengguna dengan masing-masing **tiga** *dummy* data menggunakan model yang telah dibuat pada aplikasi sebelumnya untuk setiap akun **di lokal**.
<img src='/Assets/Tugas4/Main.png'>
<img src='/Assets/Tugas4/Dummy.png'>

3. Menghubungkan model `Item` dengan `User`.
    - Pada file  `models.py` melakukan import `user`
    - Menambahkan `ForeignKey` pada `Item` di berkas `models.py` yang berfungsi untuk menghubungkan suatu barang dengan usernya
    - Pada file  `models.py`, mengubah kode pada fungsi `create_item`
    - Menambahkan parameter `commit=False` pada variable `items` yang berfungsi untuk mencegah Django agar tidak langsung menyimpan objek ke database
    - Isi field `user` dengan objek `User` dari return value `request.user` untuk menandakan bahwa objek tersebut dimiliki oleh pengguna yang sedang login

4. Menampilkan detail informasi pengguna yang sedang *logged* in seperti *username* dan menerapkan `cookies` seperti `last login` pada halaman utama aplikasi
    - Ubah fungsi `show_main`
        - Menampilkan objek yang terasosiasi dengan akun yang sedang dipakai dengan  mengganti kode di fungsi `show_main` dengan potongan kode baru `Item.objects.filter(user=request.user)`
        - Menambahkan kode `request.user.username` untuk mereturn nama sesuai akun yang dipakai
    - Pada file  `models.py` melakukan import `HttpResponseRedirect`, `reverse`, dan `datetime`
    - Menambahkan potongan kode pada fungsi `login_user`untuk melihat kapan terakhir kali pengguna melakukan *login*. Edit **blok** `if user is not None` dengan menambahkan kode:
        - `login(request, user)`
        - `response = HttpResponseRedirect(reverse("main:show_main"))`
        - `response.set_cookie('last_login', str(datetime.datetime.now()))`
        - `return response`
    - Pada fungsi `show_main` tambahkan `'last_login': request.COOKIES['last_login']` ke dalam `context`
    - Ubah fungsi `logout_user` dengan menambahkan kode:
        - `response = HttpResponseRedirect(reverse('main:login'))`
        - `response.delete_cookie('last_login')`
        - `return response`
    - Pada berkas `main.html` tambahkan kode `<h5>Sesi terakhir login: {{ last_login }}</h5>` untuk menunjukkan jam terakhir login pada situsnya

### Apa itu Django `UserCreationForm`, dan jelaskan apa kelebihan dan kekurangannya?
`UserCreationForm` adalah salah satu formulir bawaan yang disediakan oleh Django, sebuah kerangka kerja pengembangan web. Formulir ini digunakan untuk membuat formulir pendaftaran pengguna dalam aplikasi web yang dikembangkan dengan Django.

- **Kelebihan dari UserCreationForm:**
    - **Mudah Digunakan:** UserCreationForm sudah disiapkan dan dapat digunakan dengan mudah tanpa perlu menulis formulir pendaftaran dari awal. Ini menghemat waktu dan upaya dalam pengembangan aplikasi web.
    - **Integrasi dengan Django Authentication:** Formulir ini terintegrasi dengan baik dengan sistem otentikasi bawaan Django. Setelah pengguna mendaftar, data akun pengguna akan disimpan dalam database secara otomatis.
    - **Validasi Bawaan:** UserCreationForm dilengkapi dengan validasi bawaan yang memastikan bahwa pengguna harus memasukkan data yang benar dan sesuai saat mendaftar, seperti memeriksa apakah alamat username sudah digunakan.
- **Kekurangan dari UserCreationForm:**
    - **Kurang Fleksibel:** Jika aplikasi Anda memiliki persyaratan pendaftaran pengguna yang sangat khusus atau kompleks, maka UserCreationForm mungkin tidak cukup fleksibel untuk memenuhi kebutuhan tersebut.
    - **Ketergantungan pada Django:** Untuk menggunakan *UserCreationForm*, maka developer juga harus menggunakan Django sebagai frameworknya. Oleh karena itu, apabila menggunakan *framework* selain Django, maka *UserCreationForm* tidak dapat digunakan.
    - **Keterbatasan Kostumisasi**: Meskipun mudah digunakan, *UserCreationForm* memiliki fitur yang terbatas. Oleh karena itu, apabila developer ingin menambahkan fitur tambahan, maka developer harus menambahkan fitur-fitur lainnya secara manual.

### Apa perbedaan antara autentikasi dan otorisasi dalam konteks Django, dan mengapa keduanya penting?
**Autentikasi** dalam konteks Django adalah proses memeriksa apakah user yang mencoba masuk ke sistem adalah merupakan pemilik akun tersebut. Biasanya proses autentikasi melibatkan user untuk memasukkan username dan password.

**Otorisasi** adalah langkah untuk menetapkan hak akses pengguna setelah mereka berhasil terautentikasi. Fungsi dari otorisasi adalah untuk mengatur terkait akses apa saja yang dimiliki oleh user tersebut.

**Autentikasi** dan **Otorisasi** sama-sama penting untuk digunakan untuk memastikan bahwa user merupakan pengguna yang memang seharusnya masuk ke dalam suatu sistem. Kombinasi antara autentikasi dan otorisasi akan meningkatkan keamanan dari suatu sistem karena dapat mencegah user untuk melakukan hal-hal yang tidak diinginkan.

### Apa itu *cookies* dalam konteks aplikasi web, dan bagaimana Django menggunakan *cookies* untuk mengelola data sesi pengguna?
*Cookies* dpada aplikasi web merupakan sebuah bagian kecil data yang disimpan pada *device* pengguna untuk menyimpan informasi seperti preferensi web serta *ID Session*. Dengan demikian, ketika user mengakses website tersebut akan memunculkan preferensi yang sudah disimpan sebelumnya.

Django menggunakan *cookies* untuk mengelola data sesi pengguna dengan cara berikut:
- **Membuat Sesi Pengguna:** Membuat sesi pengguna serta *ID session* yang unik ketika mengakses situs.
- **Penyimpanan Data Sesi:** Menyimpan info sesi dalam cookies dan dilakukan enkripsi ketika diperlukan.
- **Mengakses Data Sesi:** Membaca cookies pengguna pada request selanjutnya.
- **Pembaruan Data Sesi:** Memungkinkan pengembang untuk meng-*update* sesi pengguna.
- **Pengakhiran Sesi:** Meng-*clear* data sesi saat selesai mengakses situs

### Apakah penggunaan *cookies* aman secara default dalam pengembangan web, atau apakah ada risiko potensial yang harus diwaspadai?
Apabila diimplementasikan dengan benar, maka penggunaan cookies secara *default* merupakan hal yang aman.
Tetapi, penggunaan *cookies* dapat menimbulkan risiko seperti pelanggaran privasi, pencurian cookies, serta rentan terhadap serangan. Oleh karena itu, diperlukan implementasi yang aman agar dapat meminimalisasi risiko dalam penggunaan *cookies*.

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
<img src='/Assets/Tugas3/SSHTML.png'>
<img src='/Assets/Tugas3/SSXML.png'>
<img src='/Assets/Tugas3/SSXMLBYID.png'>
<img src='/Assets/Tugas3/SSJSON.png'>
<img src='/Assets/Tugas3/SSJSONBYID.png'>


## Jawaban Soal Tugas 2

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

        