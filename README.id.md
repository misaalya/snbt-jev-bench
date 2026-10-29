# snbt-jev-bench

[English](README.md) · **Bahasa Indonesia**

Pengujian **TypeSafe Jev** ini memakai soal **SNBT 2025** - ujian masuk perguruan
tinggi negeri di Indonesia dari Tim Mangkuk Mi Ayam.

**Soal di sini bukan naskah asli.** Naskah SNBT tidak pernah dirilis, jadi:
> soal-soal ini disusun ulang oleh komunitas Tim MMA berdasarkan ingatan peserta.
> Redaksinya bisa berbeda dari aslinya, beberapa soal ada yang cacat, dan
> kuncinya dibuat oleh penyusun modul, bukan oleh panitia. Jadi semua angka di
> sini mengukur Jev terhadap soal rekonstruksi, bukan terhadap SNBT yang
> sebenarnya.

Dataset berisi 159 soal dari tujuh subtes. Setiap soal sudah lengkap dengan
bacaan dan transkrip gambarnya. Hasilnya ada di
**[RESULTS.id.md](RESULTS.id.md)**, dan respons mentahnya di `data/results/`.

## Sumber soal

Soal diambil dari *Modul MMA SNBT 2025* karya Tim Mangkuk Mi Ayam, berisi 764
halaman soal hasil ingatan peserta (`dataset/MMASNBT2025.pdf`, tidak disertakan
di repositori ini).

**Kuncinya tidak resmi.** Kunci dibuat oleh penyusun modul dan hanya tersedia
untuk PU, PK, dan PM. Untuk PU, penyusunnya sendiri menulis *"bukan jawaban yang
pasti"*. PPU, PBM, LBI, dan LBE tidak punya kunci.

Soal yang dipakai adalah Hari 1 Sesi 1 dari setiap subtes. PU tidak dibagi per
sesi, jadi seluruh soal PU Hari 1 dipakai.

| Subtes | | Soal | Sumber jawaban |
|---|---|---|---|
| PU | Penalaran Umum | 30 | kunci modul |
| PK | Pengetahuan Kuantitatif | 20 | kunci modul |
| PM | Penalaran Matematika | 20 | kunci modul |
| PPU | Pengetahuan dan Pemahaman Umum | 20 | label Claude |
| PBM | Pemahaman Bacaan dan Menulis | 20 | label Claude |
| LBI | Literasi Bahasa Indonesia | 29 | label Claude |
| LBE | Literasi Bahasa Inggris | 20 | label Claude |

Soal dipindahkan dari PDF ke JSON dengan bantuan **Claude Code**.

## Soal bergambar

Karena Jev hanya bisa membaca teks, semua soal bergambar **diubah menjadi teks oleh Claude Opus 5**
Ada juga hal yang disadari oleh Opus 5 selama pengerjaan dan audit, bahwa di `LBI-d1s1-teks2`, dua garis
yang awalnya disebut sejajar ternyata tidak sejajar setelah kemiringannya
diukur sehingga harus di perbaiki manual oleh Opus 5.

## Sumber jawaban acuan

**PU, PK, dan PM** yang memakai kunci jawaban modul, diperiksa ulang dengan opus 5
karena penyusunnya sendiri tidak menjamin kebenarannya.

Untuk **PPU, PBM, LBI, dan LBE5**, karena modul tidak
menyediakan kunci. Label ini hanya dari satu model, jadi belum tentu benar.

## Cara request ke Jev

Setiap soal dikirim sebagai satu request ke `POST
https://api.typesafe.ai/v1/systemone`. Semua payload ditulis dulu ke disk oleh
`scripts/build_requests.py`, jadi request di balik setiap skor bisa dicek.

Soal pilihan ganda dikirim sebagai satu **Choice** dengan opsi a sampai e dari
soal itu sendiri. Ada dua pengecualian:

- **Dua soal tabel Ya/Tidak** (PM q06 dan q14) dikirim sebagai tiga penilaian
  **Noul**, satu per pernyataan. Nilai dihitung per soal utuh. Skor per
  pernyataan dilaporkan terpisah karena peluang tebakannya 50%, bukan 20%.
- **Tiga soal isian** (PK q01, q02, q18) tidak punya opsi. Kalau dibuatkan
  opsi, yang diukur adalah opsi buatan itu, bukan modelnya. Jadi ketiganya
  tidak dipakai, dan yang dinilai 67 soal, bukan 70.

## Run skrip benchmark

```bash
python3 scripts/build_requests.py        # tulis payload request
python3 scripts/run_bench.py --dry-run   # cek tanpa mengirim

cp .env.example .env                     # lalu isi kunci API
python3 scripts/run_bench.py --limit 5   # uji cepat
python3 scripts/run_bench.py             # 67 soal berkunci modul
python3 scripts/score.py

# 89 soal lainnya, dinilai dengan label Claude
python3 scripts/build_requests.py --split claude_labeled
python3 scripts/run_bench.py --requests data/requests/claude_labeled.jsonl \
                             --out data/results/claude_labeled.jsonl
python3 scripts/score.py --agreement --results data/results/claude_labeled.jsonl

python3 scripts/score.py --combined      # semua 156 soal
```

`data/results/` sudah berisi hasil run yang dipublikasikan. Runner akan
melewati soal yang jawabannya sudah ada, jadi perintah di atas bisa dijalankan
tanpa kunci API.
Untuk run baru, simpan hasilnya ke file lain dengan
`--out data/results/rerun.jsonl`, lalu pakai path yang sama di
`scripts/score.py --results`.

Jika run terputus selama proses, cukup jalankan ulang perintahnya. Respons 408, 429, dan 5xx
otomatis dicoba ulang, dan request yang gagal tetap dicatat tanpa menghentikan
run.

`scripts/score.py` menampilkan akurasi per subtes dan per tingkat keyakinan,
skor Brier, akurasi jika jawaban paling ragu tidak dihitung, serta biaya dan
latensi.

## Ringkasan hasil

Table ini memisahkan soal yang menggunakan kunci jawaban bawaan modul dari Tim MMA,
dan kunci jawaban yang dibuat oleh Opus 5.

| Kelompok soal | Skor |
|---|---|
| 67 soal, kunci jawaban modul | 47,8% |
| 89 soal, kunci jawaban dari Claude | 85,4% |
| 156 soal, gabungan keduanya | 69,2% |

Rincian lengkap ada di **[RESULTS.id.md](RESULTS.id.md)**.

## Isi repositori

```
data/questions/with_key/        PU, PK, PM (kunci jawaban dari modul)
data/questions/claude_labeled/  PPU, PBM, LBI, LBE (kunci jawaban dari label Claude)
data/audit/                     hasil pemeriksaan kunci modul dan soal yang meragukan
data/figures/                   potongan gambar asli
data/results/                   hasil run di RESULTS.id.md, satu baris per soal
scripts/build_requests.py       mengubah soal menjadi payload request
scripts/run_bench.py            mengirim payload, mencatat jawaban dan token
scripts/score.py                menilai hasil run
```

`data/requests/` dibuat ulang secara lokal dan tidak dilacak git. Isi
`data/results/` disimpan apa adanya, jadi angka di RESULTS.id.md bisa dihitung
ulang dan dibandingkan dengan run baru.

## Atribusi

Soal: **Modul MMA SNBT 2025**, Tim Mangkuk Mi Ayam
(<https://linktr.ee/MangkukMieAyam>). Tata letaknya berlisensi CC BY-NC 4.0.
Isi soal milik penyelenggara ujian dan dipakai di sini hanya untuk riset
non-komersial.
