# Hasil

[English](RESULTS.md) · **Bahasa Indonesia**

Jev (`jev-1.13.0`, dipanggil sebagai `jev-latest`) mengerjakan 156 soal pada
18 September 2026. Satu request per soal, gambar dikirim sebagai teks. Semua
request berhasil pada percobaan pertama.

## Skor per subtes

| Subtes | Skor | Soal | Acuan |
|---|---|---|---|
| LBE (membaca bahasa Inggris) | 100,0% | 20/20 | Claude |
| LBI (membaca bahasa Indonesia) | 89,7% | 26/29 | Claude |
| PBM (membaca dan menulis) | 75,0% | 15/20 | Claude |
| PPU (pengetahuan bahasa Indonesia) | 75,0% | 15/20 | Claude |
| PU (penalaran verbal dan numerik) | 60,0% | 18/30 | MMA |
| PM (matematika terapan) | 45,0% | 9/20 | MMA |
| PK (penalaran kuantitatif) | 29,4% | 5/17 | MMA |
| Semua (campuran) | 69,2% | 108/156 | campuran |

## Score notes

- **MMA 47,8% (32/67)**: benar menurut kunci MMA. Kunci ini tidak resmi, dan
  penyusunnya sendiri menyebut kunci PU belum tentu benar. Jika dua kunci PU yang
  paling diragukan dikeluarkan, skornya jadi 49,2%.
- **Claude 85,4% (76/89)**: dapat dianggap seberapa sering Jev sepakat dengan Claude Opus 5. Tidak ada
  kunci resmi untuk subtes ini, jadi ini kecocokan, bukan akurasi.
- **Gabungan 69,2%**: ringkasan dari dua acuan dan subtes berbeda, bukan satu
  nilai ujian.

Jev kuat di bahasa dan bacaan (LBE 100%, LBI 89,7%, PBM dan PPU 75%), lemah di
kuantitatif (PK 29,4%, PM 45,0%). PU mencampur keduanya: verbal 75%, hitungan
dan grafik 30%.

## Confidence

- Saat Jev yakin 0,9-1,0: jawabannya cocok **96,7%**.
- Saat confidence Jev di bawah 0,5: jawabannya cocok **34,0%**.
- Jika 30% jawaban paling ragu dilewati: akurasi naik ke **85,2%**.

Dua soal tabel Ya/Tidak salah sebagai soal utuh (0/2), tapi 4 dari 6
pernyataannya benar.

## Catatan

- Jev dan Claude berbeda pada 13 dari 89 soal Claude. Jawaban Claude juga bukan
  kunci emas, jadi perbedaan ini belum tentu kesalahan Jev.
- Tiga soal isian (input teks) PK tidak dinilai; tersisa 67 dari 70 soal MMA.
- Gambar dikirim sebagai teks, dan hanya ada satu run pada satu versi model, jadi
  pengaruh gambar dan perkiraan galat belum diketahui.

## Biaya dan kecepatan

Run ini memakai 106.637 token input dan 8.268 token output, dengan biaya sekitar
$0,0045 pada tarif `score.py` ($0,042 per satu juta token input; cek harga
terbaru). Median respons 883 ms, persentil ke-95 1,19 detik.

## Hitung ulang

```bash
python3 scripts/score.py
python3 scripts/score.py --combined
python3 scripts/score.py --agreement --results data/results/claude_labeled.jsonl
```

Semua respons ada di `data/results/`.
