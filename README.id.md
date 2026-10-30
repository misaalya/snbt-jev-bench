# snbt-jev-bench

[English](README.md) · **Bahasa Indonesia**

Repo ini mengukur seberapa baik **Jev**, model AI dari TypeSafe, mengerjakan
soal **SNBT 2025**, ujian masuk perguruan tinggi negeri. Jev menjawab dengan
memilih satu opsi dan memberi nilai confidence. Ada dua hal yang dicek di sini:
jawabannya benar atau tidak, dan apakah nilai confidence-nya masuk akal.

**Soalnya bukan soal SNBT asli.** Naskah SNBT tidak pernah dirilis. 159 soal di
repo ini disusun ulang oleh *Modul MMA SNBT 2025* (Tim Mangkuk Mi Ayam) dari
ingatan peserta. Jadi redaksinya bisa berbeda, beberapa soal cacat, dan kuncinya
tidak resmi. Semua angka di sini mengukur Jev pada rekonstruksi ini, bukan pada
ujian sebenarnya.

## Soal dan acuan jawaban

| Subtes | Isi | Soal | Acuan |
|---|---|---|---|
| PU | Penalaran verbal dan numerik | 30 | MMA |
| PK | Penalaran kuantitatif | 20 | MMA |
| PM | Matematika terapan | 20 | MMA |
| PPU | Pengetahuan bahasa Indonesia | 20 | Claude |
| PBM | Membaca dan menulis | 20 | Claude |
| LBI | Membaca bahasa Indonesia | 29 | Claude |
| LBE | Membaca bahasa Inggris | 20 | Claude |

Tiga subtes pertama punya kunci MMA. Empat sisanya tidak, jadi acuannya
jawaban dari Claude Opus 5. Artinya:

- Skor **MMA** = benar menurut kunci MMA. Kunci ini tidak resmi, dan
  penyusunnya sendiri menyebut kunci PU belum tentu benar.
- Skor **Claude** = seberapa sering Jev setuju dengan Claude, bukan akurasi
  resmi.

Kenapa hanya 156 soal yang dinilai? Tiga soal PK (q01, q02, q18) adalah soal
isian (input teks). Jev adalah model decision dan tidak bisa menulis
jawaban bebas seperti LLM. Sehingga ketiga soal itu tidak cocok
dengan bentuk pengujian ini, jadi dilewati.

## Pengujian

Setiap soal dikirim sebagai satu request, dan soal pilihan ganda memakai opsi
asli. Dua soal PM berupa tabel Ya/Tidak. Contoh bentuknya (PM q06):

*Klik pilihan kolom di sebelah kanan pernyataan yang sesuai dengan jawaban
(Ya / Tidak).*

| Pernyataan | Ya | Tidak |
|---|---|---|
| Bilangan pada petak ke-3 baris ke-2 adalah 512. |  |  |
| Jumlah bilangan pada petak pertama sampai ke-5 baris pertama adalah 31. |  |  |
| Bilangan pada baris ke-8 petak ke-8 adalah 2^64. |  |  |

Setiap baris pertanyaan dikirim ke Jev sebagai satu keputusan Ya/Tidak. Soal dihitung
benar hanya jika ketiganya benar. Request dan respons disimpan, jadi hasilnya
bisa dicek ulang.

## Hasil

- 67 soal MMA: **47,8% benar** (32/67).
- 89 soal Claude: **85,4% sepakat** (76/89).
- 156 soal gabungan: **69,2%** (108/156).

Angka 69,2% hanya ringkasan karena mencampur dua acuan dan subtes yang berbeda.
Yang lebih berguna:

- Jev kuat di bahasa dan bacaan: LBE 100%, LBI 89,7%, PBM dan PPU 75%.
- Jev lemah di kuantitatif: PK 29,4%, PM 45,0%.
- Di PU, soal verbal 75%, sedangkan soal hitungan dan grafik 30%.

Confidence Jev juga berguna: saat yakin 0,9-1,0, jawabannya cocok 96,7%; saat
confidence-nya di bawah 0,5, hanya 34,0%. Jika 30% jawaban paling ragu dilewati,
akurasi naik ke 85,2%.

Detail lengkap, termasuk biaya dan batasan, ada di
**[RESULTS.id.md](RESULTS.id.md)**.

## Menjalankan ulang

```bash
cp .env.example .env                         # isi TYPESAFE_API_KEY
python3 scripts/build_requests.py
python3 scripts/run_bench.py                 # 67 soal MMA
python3 scripts/score.py

python3 scripts/build_requests.py --split claude_labeled
python3 scripts/run_bench.py --requests data/requests/claude_labeled.jsonl \
                             --out data/results/claude_labeled.jsonl
python3 scripts/score.py --agreement --results data/results/claude_labeled.jsonl

python3 scripts/score.py --combined          # semua 156 soal
```

Penilaian ulang tidak membutuhkan API key: hasil run sudah ada di
`data/results/`, dan runner akan melewati soal yang sudah dijawab. Skripnya
hanya butuh pustaka standar Python. Untuk run baru, hasilnya disimpan ke file
lain dengan `--out`, lalu file itu diberikan ke `score.py --results`.

## Atribusi

Soal: **Modul MMA SNBT 2025**, Tim Mangkuk Mi Ayam
(<https://linktr.ee/MangkukMieAyam>). Tata letaknya berlisensi CC BY-NC 4.0.
Isi soal milik penyelenggara ujian dan dipakai di sini hanya untuk riset
non-komersial. Kode benchmark berlisensi MIT; lihat [LICENSE](LICENSE).
