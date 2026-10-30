# snbt-jev-bench

**English** · [Bahasa Indonesia](README.id.md)

This repo measures how well **Jev**, TypeSafe's AI model, handles **SNBT 2025**,
Indonesia's university entrance exam. Jev answers by picking one option and
giving a confidence score. There are two things to check here: whether the
answer is right, and whether the confidence makes sense.

**These are not the real SNBT questions.** SNBT papers are never released. The
159 questions here were rebuilt by *Modul MMA SNBT 2025* (Tim Mangkuk Mi Ayam)
from what test-takers remembered. So the wording may differ, a few questions
are flawed, and the answer keys are unofficial. Every number here measures Jev
on this reconstruction, not on the real exam.

## Questions and reference answers

| Subtest | What it tests | Questions | Reference |
|---|---|---|---|
| PU | Verbal and numerical reasoning | 30 | MMA |
| PK | Quantitative reasoning | 20 | MMA |
| PM | Applied mathematics | 20 | MMA |
| PPU | Indonesian language knowledge | 20 | Claude |
| PBM | Reading and writing | 20 | Claude |
| LBI | Indonesian reading | 29 | Claude |
| LBE | English reading | 20 | Claude |

The first three subtests have an MMA key. The other four do not, so their
reference answers come from Claude Opus 5. That means:

- An **MMA** score means "correct according to the MMA key". The key is
  unofficial, and the module's authors say the PU answers are uncertain.
- A **Claude** score means "how often Jev agrees with Claude". It is not
  official accuracy.

Why are only 156 of the 159 questions scored? Three PK questions (q01, q02,
q18) are fill-in (text-input) questions. Jev is tested here as a decision model:
it chooses from given options instead of writing a free-form answer. Those
questions do not fit this test format, so they are skipped.

## How Jev was tested

One request per question, and multiple-choice questions use their own options.
Two PM questions are Ya/Tidak tables. Here is the shape (PM q06; the text is in
Indonesian):

*Klik pilihan kolom di sebelah kanan pernyataan yang sesuai dengan jawaban
(Ya / Tidak).* (Click the column next to each statement that matches the
answer: Yes / No.)

| Pernyataan | Ya | Tidak |
|---|---|---|
| Bilangan pada petak ke-3 baris ke-2 adalah 512. |  |  |
| Jumlah bilangan pada petak pertama sampai ke-5 baris pertama adalah 31. |  |  |
| Bilangan pada baris ke-8 petak ke-8 adalah 2^64. |  |  |

Each statement is sent to Jev as a separate Ya/Tidak decision. The question
counts as correct only if all three are right. The requests and responses are
kept, so any score can be checked.

## Results

- 67 MMA questions: **47.8% correct** (32/67).
- 89 Claude questions: **85.4% agreement** (76/89).
- All 156 questions: **69.2%** (108/156).

The 69.2% is only a summary, because it mixes two references and different
subtests. More useful:

- Jev is strong in language and reading: LBE 100%, LBI 89.7%, PBM and PPU 75%.
- Jev is weak in quantitative questions: PK 29.4%, PM 45.0%.
- Inside PU, verbal questions score 75%, while math and chart questions score
  30%.

Confidence is informative: when Jev is 0.9-1.0 confident, its answer matches
the reference 96.7% of the time; below 0.5, only 34.0%. Skipping the 30% least
confident answers raises accuracy to 85.2%.

Full details, including cost and limitations, are in
**[RESULTS.md](RESULTS.md)**.

## Reproduce

```bash
cp .env.example .env                         # add TYPESAFE_API_KEY
python3 scripts/build_requests.py
python3 scripts/run_bench.py                 # 67 MMA items
python3 scripts/score.py

python3 scripts/build_requests.py --split claude_labeled
python3 scripts/run_bench.py --requests data/requests/claude_labeled.jsonl \
                             --out data/results/claude_labeled.jsonl
python3 scripts/score.py --agreement --results data/results/claude_labeled.jsonl

python3 scripts/score.py --combined          # all 156 items
```

The scripts need only Python's standard library. Rescoring does not need an API
key: the published run is already in `data/results/`, and the runner skips
questions that already have an answer. For a new run, results go to another
file set with `--out` and passed to `score.py --results`.

## Attribution

Questions: **Modul MMA SNBT 2025**, Tim Mangkuk Mi Ayam
(<https://linktr.ee/MangkukMieAyam>). The typesetting is licensed CC BY-NC 4.0.
The question content belongs to the exam organiser and is used here for
non-commercial research only. The benchmark code is MIT-licensed; see
[LICENSE](LICENSE).
