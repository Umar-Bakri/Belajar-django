1. Pola Kerja MVT (Model, View, Template)
Model (models.py): Desain tabel database (blueprint data).
contoh code di models.py
from django.db import models

class Project(models.Model):
    judul = models.CharField(max_length=200)
    deskripsi = models.TextField()
    teknologi = models.CharField(max_length=200)
    
    def __str__(self):
        return self.judul

View (views.py): Dapur logika backend (ambil data dari DB, proses form, kirim rute).
contoh code di views.py 
def Portofolio(request):
    all_data_db = Project.objects.all()
    
    konteks_data = {
        'daftar_portofolio' : all_data_db
    }
    return render(request,'portofolio/daftar.html', konteks_data)

Template (HTML): Baju kosmetik/tampilan depan web yang dilihat user.

2. Perintah Migrasi Database
python manage.py makemigrations : Mencatat dan menerjemahkan perubahan kode di models.py menjadi instruksi database.

python manage.py migrate : Menembakkan instruksi tadi langsung ke file database (db.sqlite3).

3. Logika Dapur CRUD (views.py)
Create: Model.objects.create(kolom=data)

Read (Semua): Model.objects.all()

Read (Detail/Satu): Model.objects.get(id=id)

def detail_proyek(request, id):
    proyek_tunggal = Project.objects.get(id=id)
    
    konteks_data = {
        'proyek': proyek_tunggal
    }
    return render(request, 'portofolio/detail.html', konteks_data)

    # 🎯 LANGKAH 2: Logika untuk menangkap inputan dari form browser
@login_required
def tambah_proyek(request):
    # Jika user menekan tombol "Simpan" (Mengirim data/POST)
    if request.method == 'POST':
        # Tangkap apa yang diketik user di kolom input HTML
        judul_baru = request.POST.get('judul')
        deskripsi_baru = request.POST.get('deskripsi')
        teknologi_baru = request.POST.get('teknologi')
        # Perintah sakti untuk langsung memasukkan data ke database
        Project.objects.create(
            judul =judul_baru,
            deskripsi=deskripsi_baru,
            teknologi=teknologi_baru
        )
        # Setelah sukses menyimpan, lempar user kembali ke halaman daftar portofolio
        return redirect('portofolio')
    # Jika user cuma sekadar buka halaman biasa (GET), tampilin form kosong
    return render(request, 'portofolio/tambah.html')

Update: Ambil data lama lewat .get(), timpa nilainya, lalu panggil .save().
@login_required
def edit_proyek(request, id):
  1. Ambil data proyek lama dari database berdasarkan ID
  proyek_lama = Project.objects.get(id=id)
  
  2. Jika user menekan tombol "Simpan Perubahan" (POST)
  if request.method == 'POST':
   #Tindih data lama dengan apa yang diketik baru oleh user
   proyek_lama.judul = request.POST.get('judul')
   proyek_lama.teknologi = request.POST.get('teknologi')
   proyek_lama.deskripsi = request.POST.get('deskripsi')
   
   #Simpan perubahan tersebut ke database
   proyek_lama.save()
   #Lempar balik user ke halaman detail proyek yang baru di-edit
   return redirect('detail_proyek', id=proyek_lama.id)

 #3. Jika user cuma buka halaman biasa (GET), tampilkan form yang berisi data lama
  konteks_data = {
    'proyek': proyek_lama
}  
 
  return render(request, 'portofolio/edit.html', konteks_data)

Delete: Ambil data target lewat .get(), lalu hancurkan dengan perintah .delete().

@login_required
def hapus_proyek(request, id):
    # Cari proyek yang mau dihapus berdasarkan ID
    proyek_yang_mau_dihapus = Project.objects.get(id=id)
    # Perintah sakti untuk menghapusnya dari database
    proyek_yang_mau_dihapus.delete()
    
    # Setelah terhapus, lempar balik user ke halaman utama portofolio
    return redirect('portofolio')

4. Gembok Keamanan Sakral
GET vs POST: GET untuk meminta halaman biasa/kosong. POST untuk mengirimkan data sensitif dari Form ke database.

{% csrf_token %} : Token wajib di bawah tag <form> HTML agar web aman dari serangan injeksi paket palsu.

@login_required : Gembok dekorator di atas fungsi views.py biar halaman CRUD cuma bisa diutak-atik oleh admin yang sudah login.

Pencegahan Celah IDOR: Mengamankan tombol aksi agar tidak bisa ditembak hacker lewat utak-atik angka ID di URL browser tanpa login resmi.

