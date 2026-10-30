# PySpark — pandas ke Spark & ETL Superstore

## Yang perlu di-install

- [Miniconda](https://www.anaconda.com/download/success)
- [Git](https://git-scm.com/downloads)
- PostgreSQL + pgAdmin
- [VS Code](https://code.visualstudio.com/) + extension **Python** dan **Jupyter**

## Install

Windows: jalankan di **Anaconda Prompt**. Mac: **Terminal**.

```bash
git clone https://github.com/Fredyco/pyspark.git
cd pyspark

conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main   # error "invalid choice: 'tos'"? abaikan
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r
conda create -n dbt313 python=3.13 pip -y      # lewati kalau env dbt313 sudah ada
conda activate dbt313
conda install -c conda-forge openjdk=21 -y
python -m pip install -r requirements.txt

java -version                                   # openjdk version "21..."
python -c "import pyspark; print(pyspark.__version__)"   # 4.2.1
```

Salin `.env.example` menjadi `.env`, lalu isi `PG_PASSWORD`.

## Menjalankan

1. pgAdmin: jalankan `sql/01_create_database.sql` (di database `postgres`), lalu `sql/02_create_tables.sql` (di database `dwh`).
2. VS Code: buka folder `pyspark` → buka notebook → **Select Kernel** → `dbt313`.
   Atau browser: `python -m jupyterlab`
3. Jalankan `notebooks/01_pandas_vs_spark.ipynb`, lalu `notebooks/02_etl_superstore.ipynb`.
4. Cek hasil: `sql/03_check.sql` di pgAdmin.

Contekan sintaks: `cheatsheet_pandas_spark.html` (buka di browser).

> Selalu pakai `python -m ...` (`python -m pip`, `python -m jupyterlab`), bukan `pip` / `jupyter` langsung,
> supaya tidak diblokir *Device Guard* di laptop Windows.
