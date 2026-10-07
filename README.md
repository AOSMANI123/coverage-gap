# Coverage Gap Detector

Measures whether a daily news brief covered what was actually available. Instead of checking what's *in* the brief, it checks what's *missing* from it.

---

## What it does

Give it a roster of people and companies you track, and a brief. It returns:

- **A coverage score (0–100):** the share of widely reported stories the brief included
- **An entity breakdown:** stories available vs. covered for each person or company
- **A missed-consensus report:** stories two or more outlets carried that the brief left out

It scores any brief, whether a person, an AI or both wrote it. It doesn't write briefs; it grades them.

---

## How it works

```
 roster.json (who to track)
        │
        ▼
 sweep public RSS feeds ──► stories that mention someone on the roster
        │
        ▼
 deduplicate syndicated stories
 (one AP story in 12 outlets counts once)
        │
        ▼
 story universe: what was available
        │
 your brief ──►  match brief against the universe
        │
        ▼
 coverage score · entity breakdown · missed-consensus report
```

---

## Repo contents

```
coverage-gap/
├── src/detector.py          # Sweep, dedupe and scoring
├── sample-roster.json       # Example roster of public company CEOs
├── evals/test_detector.py   # Tests for dedupe and scoring
├── docs/
│   ├── case-study.md        # The full PRFAQ
│   ├── metrics.md           # Metric definitions and known limits
│   └── decisions/
│       └── ADR-001-evaluator-not-generator.md
└── CHANGELOG.md
```

---

## Prerequisites

- Python 3.9 or newer
- Internet access for the RSS sweep (the tests run offline)
- Optional: `pip install feedparser` for more forgiving feed parsing. The tool falls back to the standard library without it.

---

## Setup and run

### 1. Get it

```bash
git clone https://github.com/AOSMANI123/coverage-gap.git
cd coverage-gap
```

### 2. See what stories are available

```bash
python3 src/detector.py --roster sample-roster.json --sweep-only
```

### 3. Score a brief

```bash
python3 src/detector.py --roster sample-roster.json --brief path/to/brief.txt
```

Add `--output-json` for machine-readable results.

### 4. Track your own list

Copy `sample-roster.json`, replace the names, aliases and companies, and point `--roster` at your file.

### 5. Run the tests

```bash
python3 -m unittest discover -s evals
```

---

## Prompts

None. Scoring is deterministic: title similarity for dedupe and entity matching for coverage. The [decision record](docs/decisions/ADR-001-evaluator-not-generator.md) explains why I built the evaluator and not the generator.

---

## Limits

- The score is relative to the sources you configure. A 90 means "90% of what we could see," not "90% of everything that happened."
- Every story counts equally. A resignation and a minor product update are both one story.

---

## Related

- [Full case study](docs/case-study.md)
- [Engagement Agents](https://github.com/AOSMANI123/engagement-agents): the multi-agent system where a coverage check sits alongside briefing production
- [Portfolio](https://aosmani123.github.io)
