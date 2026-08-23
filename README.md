# TISE Live Demo — Iterative Q-Cycle

Versi ini memperbaiki loop demo TISE sehingga voting/feedback audience **benar-benar kembali ke Q-Cycle**.

## Core flow

```text
Audience messages
      ↓
Collective Intelligence
      ↓
Q-Cycle 1: Q1 → Q2 → Q3 → Q4
      ↓
Find least-complete Q + critical gap
      ↓
Ask audience / vote A-B-C
      ↓
Votes + new comments become new evidence
      ↓
Q-Cycle 2: revise and re-score Q1 → Q2 → Q3 → Q4
      ↓
repeat ...
      ↓
STOP only when Q1, Q2, Q3, Q4 pass completion gate
```

## Completion gate

Default gate:

- score >= 85/100, AND
- `critical_gaps` is empty.

Threshold can be changed in `.env`:

```text
TISE_COMPLETENESS_THRESHOLD=85
```

The final `complete` and `all_complete` values are normalized in Python, not accepted blindly from the LLM.

## What is persisted

SQLite now contains:

- `messages`: audience comments/evidence
- `votes`: one current vote per participant per cycle
- `cycles`: every Q1-Q4 analysis snapshot and message cutoff
- `settings`: active vote and challenge state

This gives an audit trail of how the collective intelligence changes the engineering description.

## Telegram voting

When a vote is open, audience can send:

```text
A
B
C
```

or:

```text
/vote A
```

The same participant can change their vote; the latest choice replaces the previous one for that cycle.

A normal Telegram message is treated as qualitative evidence for the next iteration.

## Install

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Copy environment file:

```bash
cp .env.example .env
```

Example:

```text
LLAMA_SERVER=http://100.110.236.59:8088
TELEGRAM_BOT_TOKEN=YOUR_BOT_TOKEN
TELEGRAM_ACK=true
TELEGRAM_DROP_PENDING=true
TISE_COMPLETENESS_THRESHOLD=85
```

## Test llama.cpp

```bash
curl http://100.110.236.59:8088/health
```

## Run

```bash
streamlit run app.py
```

## Auditorium operation

1. Presenter defines the challenge.
2. Audience sends initial evidence through Telegram.
3. Presenter clicks **START Q-CYCLE 1**.
4. TISE creates Q1-Q4 and scores completeness.
5. TISE automatically focuses on the least-complete Q.
6. Audience votes A/B/C and may add reasons/comments.
7. Presenter clicks **CLOSE FEEDBACK & RUN Q-CYCLE N+1**.
8. Vote result + new comments + previous Q descriptions go back into llama.cpp.
9. Q1-Q4 are revised and re-scored.
10. Repeat until all Q pass the completion gate.

The pause between cycles is intentional: TISE should not hallucinate completeness by repeatedly asking itself. A new iteration requires **new human evidence** (vote or comment).
