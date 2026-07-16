# 📘 Catatan Belajar Django: Terminal & Setup Environment

Ringkasan perintah dasar terminal, setup virtual environment, dan langkah awal membuat proyek Django.

---

## 1. Perintah Sakral Terminal (Navigasi) 🧭

| Perintah | Fungsi |
|---|---|
| `cd nama_folder` | Masuk ke dalam folder |
| `cd ..` | Mundur satu folder ke belakang |
| `ls` (Mac/Linux) atau `dir` (Windows) | Mengintip semua isi file/folder di direktori saat ini |

---

## 2. Membangun "Medan Perang" (Environment & Django) ⚔️

| Perintah | Fungsi |
|---|---|
| `python -m venv myenv` | Membuat ruang isolasi virtual bernama `myenv` biar library gak bentrok |
| `myenv\Scripts\activate` | Mengaktifkan isolasi virtual (muncul tanda `(myenv)` di terminal) |
| `pip install django` | Mengunduh framework Django ke dalam environment |

---

## 3. Membuat Proyek & Aplikasi 🏗️

| Perintah | Fungsi |
|---|---|
| `django-admin startproject inti_proyek .` | Bikin pondasi utama proyek Django (tanda titik `.` artinya dibuat langsung di folder saat ini) |
| `python manage.py startapp main` | Bikin satu bilik aplikasi modular bernama `main` |
| `python manage.py runserver` | Menyalakan server lokal web Django |

---

> 📌 *Catatan pribadi belajar Django — Terminal & Setup Environment.*
