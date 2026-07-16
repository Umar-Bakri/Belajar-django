# 📘 Catatan Belajar Django: Pola MVT & CRUD

Ringkasan konsep dasar Django — pola MVT, migrasi database, operasi CRUD, hingga keamanan dasar.

---

## 1. Pola Kerja MVT (Model, View, Template)

Django menggunakan pola **MVT** (Model, View, Template) untuk memisahkan logika data, proses, dan tampilan.

### 🧱 Model (`models.py`)
Desain tabel database (blueprint data).

```python
from django.db import models

class Project(models.Model):
    judul = models.CharField(max_length=200)
    deskripsi = models.TextField()
    teknologi = models.CharField(max_length=200)

    def __str__(self):
        return self.judul
```

### 🍳 View (`views.py`)
Dapur logika backend — mengambil data dari database, memproses form, dan mengatur rute.

```python
def Portofolio(request):
    all_data_db = Project.objects.all()

    konteks_data = {
        'daftar_portofolio': all_data_db
    }
    return render(request, 'portofolio/daftar.html', konteks_data)
```

### 👕 Template (HTML)
Baju kosmetik / tampilan depan web yang dilihat oleh user.

---

## 2. Perintah Migrasi Database

| Perintah | Fungsi |
|---|---|
| `python manage.py makemigrations` | Mencatat & menerjemahkan perubahan kode di `models.py` menjadi instruksi database |
| `python manage.py migrate` | Menembakkan instruksi tadi ke file database (`db.sqlite3`) |

---

## 3. Logika Dapur CRUD (`views.py`)

### ➕ Create
```python
Model.objects.create(kolom=data)
```

### 📖 Read — Semua Data
```python
Model.objects.all()
```

### 📄 Read — Detail / Satu Data
```python
def detail_proyek(request, id):
    proyek_tunggal = Project.objects.get(id=id)

    konteks_data = {
        'proyek': proyek_tunggal
    }
    return render(request, 'portofolio/detail.html', konteks_data)
```

### 📝 Tambah Data (`tambah_proyek`)
Logika untuk menangkap inputan dari form browser.

```python
@login_required
def tambah_proyek(request):
    # Jika user menekan tombol "Simpan" (mengirim data via POST)
    if request.method == 'POST':
        # Tangkap apa yang diketik user di kolom input HTML
        judul_baru = request.POST.get('judul')
        deskripsi_baru = request.POST.get('deskripsi')
        teknologi_baru = request.POST.get('teknologi')

        # Perintah untuk langsung memasukkan data ke database
        Project.objects.create(
            judul=judul_baru,
            deskripsi=deskripsi_baru,
            teknologi=teknologi_baru
        )
        # Setelah sukses menyimpan, lempar user kembali ke halaman daftar portofolio
        return redirect('portofolio')

    # Jika user cuma buka halaman biasa (GET), tampilkan form kosong
    return render(request, 'portofolio/tambah.html')
```

### ✏️ Update
Ambil data lama lewat `.get()`, timpa nilainya, lalu panggil `.save()`.

```python
@login_required
def edit_proyek(request, id):
    # 1. Ambil data proyek lama dari database berdasarkan ID
    proyek_lama = Project.objects.get(id=id)

    # 2. Jika user menekan tombol "Simpan Perubahan" (POST)
    if request.method == 'POST':
        # Tindih data lama dengan apa yang diketik baru oleh user
        proyek_lama.judul = request.POST.get('judul')
        proyek_lama.teknologi = request.POST.get('teknologi')
        proyek_lama.deskripsi = request.POST.get('deskripsi')

        # Simpan perubahan tersebut ke database
        proyek_lama.save()
        # Lempar balik user ke halaman detail proyek yang baru di-edit
        return redirect('detail_proyek', id=proyek_lama.id)

    # 3. Jika user cuma buka halaman biasa (GET), tampilkan form berisi data lama
    konteks_data = {
        'proyek': proyek_lama
    }
    return render(request, 'portofolio/edit.html', konteks_data)
```

### 🗑️ Delete
Ambil data target lewat `.get()`, lalu hancurkan dengan `.delete()`.

```python
@login_required
def hapus_proyek(request, id):
    # Cari proyek yang mau dihapus berdasarkan ID
    proyek_yang_mau_dihapus = Project.objects.get(id=id)
    # Perintah untuk menghapusnya dari database
    proyek_yang_mau_dihapus.delete()

    # Setelah terhapus, lempar balik user ke halaman utama portofolio
    return redirect('portofolio')
```

---

## 4. Gembok Keamanan Sakral 🔒

| Konsep | Penjelasan |
|---|---|
| **GET vs POST** | `GET` untuk meminta halaman biasa/kosong. `POST` untuk mengirimkan data sensitif dari form ke database. |
| **`{% csrf_token %}`** | Token wajib di bawah tag `<form>` HTML agar web aman dari serangan injeksi paket palsu. |
| **`@login_required`** | Dekorator gembok di atas fungsi `views.py` agar halaman CRUD hanya bisa diakses oleh admin yang sudah login. |
| **Pencegahan Celah IDOR** | Mengamankan tombol aksi agar tidak bisa "ditembak" hacker lewat utak-atik angka ID di URL browser tanpa login resmi. |

---

> 📌 *Catatan pribadi belajar Django — CRUD & keamanan dasar.*
