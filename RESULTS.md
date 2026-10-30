# Results

**English** · [Bahasa Indonesia](RESULTS.id.md)

Jev (`jev-1.13.0`, served as `jev-latest`) answered 156 questions on
18 September 2026. One request per question, figures sent as text. Every request
succeeded on the first try.

## Score by subtest

| Subtest | Score | Items | Reference |
|---|---|---|---|
| LBE (English reading) | 100.0% | 20/20 | Claude |
| LBI (Indonesian reading) | 89.7% | 26/29 | Claude |
| PBM (reading and writing) | 75.0% | 15/20 | Claude |
| PPU (Indonesian language knowledge) | 75.0% | 15/20 | Claude |
| PU (verbal and numerical reasoning) | 60.0% | 18/30 | MMA |
| PM (applied mathematics) | 45.0% | 9/20 | MMA |
| PK (quantitative reasoning) | 29.4% | 5/17 | MMA |
| All (mixed) | 69.2% | 108/156 | mixed |

## What the numbers mean

- **MMA 47.8%**: correct according to the MMA key. The key is unofficial, and
  the module's authors say the PU answers are uncertain. Dropping the two most
  disputed PU keys gives 49.2%.
- **Claude 85.4%**: how often Jev agrees with Claude Opus 5. There is no
  official key for these subtests, so this is agreement, not accuracy.
- **Combined 69.2%**: a summary of two references and different subtests, not
  one exam score.
- For scale, random guessing would score about 20% on most questions.

Jev is strong in language and reading (LBE 100%, LBI 89.7%, PBM and PPU 75%),
and weak in quantitative questions (PK 29.4%, PM 45.0%). PU mixes both: 75% on
verbal questions, 30% on math and chart questions.

## Confidence

- At 0.9-1.0 confidence, the answer matched the reference **96.7%** of the time.
- Below 0.5 confidence, it matched **34.0%** of the time.
- Skipping the 30% least confident answers raises accuracy to **85.2%**.

Both Ya/Tidak tables are wrong as whole questions (0/2), but 4 of their 6
statements are correct.

## Caveats

- Jev and Claude disagree on 13 of the 89 Claude questions. Claude's answers are
  not a gold standard, so these are not automatically Jev mistakes.
- Three PK fill-in (text-input) questions are not scored; 67 of 70 MMA questions
  remain.
- Figures were sent as text, and there is only one run of one model version, so
  the effect of the figures and the error range are unknown.

## Cost and speed

The run used 106,637 input tokens and 8,268 output tokens, costing about $0.0045
at the rate in `score.py` ($0.042 per million input tokens; check current
pricing). The median response took 883 ms, and 95% finished within 1.19 s.

## Recompute

```bash
python3 scripts/score.py
python3 scripts/score.py --combined
python3 scripts/score.py --agreement --results data/results/claude_labeled.jsonl
```

All responses are in `data/results/`.
