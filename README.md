# PySpark — pandas ke Spark & ETL Superstore

Materi PySpark yang dijalankan langsung dengan **conda**, tanpa Docker.

- `01_pandas_vs_spark.ipynb` → operasi pandas yang sudah dikenal dan versi Spark-nya.
- `02_etl_superstore.ipynb` → ETL 3 file CSV Superstore ke star schema di PostgreSQL. Database & tabel dibuat dengan SQL.
- `cheatsheet_pandas_spark.html` → contekan sintaks pandas ↔ Spark. Buka dengan klik dua kali (browser).

Butuh: conda, PostgreSQL + pgAdmin di laptop, ±1,2 GB ruang disk.
Versi Docker (PySpark + Airflow): [Fredyco/airflow_repo](https://github.com/Fredyco/airflow_repo).

## Peta folder

```
pyspark/
├── cheatsheet_pandas_spark.html  ← contekan pandas ↔ Spark (buka di browser)
├── requirements.txt              ← langkah 1 (daftar library)
├── .env.example → .env           ← langkah 2
├── sql/
│   ├── 01_create_database.sql    ← langkah 3
│   ├── 02_create_tables.sql      ← langkah 3
│   └── 03_check.sql              ← langkah 5
├── data/                         ← 3 file CSV Superstore
└── notebooks/
    ├── 01_pandas_vs_spark.ipynb  ← langkah 4
    └── 02_etl_superstore.ipynb   ← langkah 4
```

## 1. Install

### 1.1 Install conda & git (lewati kalau sudah ada)

Download & install **Miniconda**: https://www.anaconda.com/download/success (pilih *Miniconda Installer* sesuai OS).

- **Windows:** setelah install, buka **Anaconda Prompt** dari Start Menu. Semua perintah di bawah dijalankan di sana.
- **Mac:** buka **Terminal**. Kalau `conda` belum dikenal, tutup lalu buka lagi Terminal.

Install **git** juga kalau belum ada: https://git-scm.com/downloads (Mac: ketik `git` di Terminal, lalu ikuti instruksi install).

Cek:

```bash
conda --version
git --version
```

### 1.2 Download materi

```bash
git clone https://github.com/Fredyco/pyspark.git
cd pyspark
```

Tidak pakai git? Di halaman GitHub klik **Code → Download ZIP**, lalu extract.

### 1.3 Buat env `dbt313` (lewati kalau sudah ada dari materi dbt)

```bash
conda create -n dbt313 -c conda-forge python=3.13 pip -y
```

Cek env yang sudah ada: `conda env list`.

### 1.4 Aktifkan env, install Java & library

**Terminal** (di folder `pyspark`):

```bash
conda activate dbt313

conda install -c conda-forge openjdk=21 -y
pip install pyspark==4.2.1 "pandas>=2.2.3,<3" psycopg2-binary python-dotenv jupyterlab
```

- `conda activate dbt313` → semua yang di-install masuk ke env ini. Pastikan nama env muncul di awal baris terminal: `(dbt313)`.
- `openjdk=21` → Java, wajib untuk Spark. Di-install lewat conda supaya tidak perlu install Java manual.
- `pyspark` → Spark untuk Python (±450 MB, bagian paling lama).
- `pandas` → versi 2.x, yang cocok dengan PySpark 4.2.
- `psycopg2-binary` → koneksi ke PostgreSQL.
- `python-dotenv` → membaca file `.env`.
- `jupyterlab` → Jupyter (juga dibutuhkan VS Code untuk menjalankan notebook).
- Daftar library yang sama ada di `requirements.txt`, jadi bisa juga: `pip install -r requirements.txt`.

### 1.5 Cek instalasi

```bash
java -version
python -c "import pyspark; print(pyspark.__version__)"
```

Berhasil kalau muncul `openjdk version "21..."` dan `4.2.1`.

## 2. Isi `.env`

```bash
cp .env.example .env              # Mac / Linux
Copy-Item .env.example .env       # Windows (PowerShell)
```

Buka `.env`, isi `PG_PASSWORD` dengan password pgAdmin. `PG_HOST=localhost` karena notebook jalan langsung di laptop.

## 3. Buat database & tabel (pgAdmin)

1. Klik kanan server → *Query Tool* (database `postgres`) → buka `sql/01_create_database.sql` → jalankan.
2. Klik kanan *Databases* → *Refresh* → pilih `dwh` → *Query Tool* → buka `sql/02_create_tables.sql` → jalankan.

## 4. Jalankan notebook

Pilih salah satu cara.

**A. VS Code (disarankan)**

1. Install extension **Python** dan **Jupyter** di VS Code.
2. *File → Open Folder* → pilih folder `pyspark`.
3. Buka `notebooks/01_pandas_vs_spark.ipynb`.
4. Klik **Select Kernel** (kanan atas) → *Python Environments* → pilih **`dbt313`**.
   Env tidak muncul? `Ctrl/Cmd + Shift + P` → *Python: Select Interpreter* → pilih `dbt313`, lalu ulangi.
5. Jalankan cell dengan `Shift + Enter`.

**B. JupyterLab di browser**

**Terminal** (di folder `pyspark`, env sudah aktif):

```bash
jupyter lab
```

Masuk folder `notebooks`. Tekan `Ctrl + C` di terminal untuk mematikan Jupyter.

Jalankan berurutan:

1. `01_pandas_vs_spark.ipynb` → syntax pandas vs Spark.
2. `02_etl_superstore.ipynb` → ETL ke PostgreSQL. Saat pertama jalan, driver PostgreSQL di-download otomatis (butuh internet).

## 5. Cek hasil

Di pgAdmin, database `dwh` → *Query Tool* → buka `sql/03_check.sql` → jalankan.
Untuk mengulang dari awal: jalankan lagi `02_create_tables.sql` (tabel dikosongkan), lalu jalankan ulang notebook 02.

## Kalau ada masalah

| Masalah | Solusi |
| --- | --- |
| `JAVA_GATEWAY_EXITED` / Java tidak ditemukan | Pastikan kernel/env yang dipilih `dbt313` dan Java sudah terpasang di env itu: `conda activate dbt313` → `java -version`. Kalau tidak ada: `conda install -c conda-forge openjdk=21 -y`, lalu restart kernel |
| `jupyter: command not found` | Env belum aktif. `conda activate dbt313` dulu |
| VS Code: kernel `dbt313` tidak ada | Pastikan extension Python & Jupyter terpasang, lalu *Python: Select Interpreter* → `dbt313` |
| `Belum diisi di file .env` | Buat `.env` dari `.env.example`, lalu di Jupyter: *Kernel → Restart Kernel* |
| `Password salah` | Samakan `PG_PASSWORD` di `.env` dengan password pgAdmin, lalu restart kernel |
| Gagal download driver PostgreSQL | Butuh internet saat pertama kali menjalankan notebook 02 |
| Windows: pesan tentang `winutils.exe` / `HADOOP_HOME` | Peringatan ini boleh diabaikan; notebook ini tidak menulis file ke disk |

## Ringkasan perintah

| Perintah | Fungsi |
| --- | --- |
| `conda activate dbt313` | Aktifkan env (setiap buka terminal baru) |
| `jupyter lab` | Buka Jupyter |
| `java -version` | Cek Java |
| `conda deactivate` | Keluar dari env |
