# TISE Live Demo

Audience sends free-text input through Telegram. Streamlit collects it into
SQLite and sends the collective dataset to a llama.cpp server. The model then
structures the result into Q1-Q4 and returns a question to the audience.

Default llama.cpp server:

```text
http://100.110.236.59:8088
```

## 1. Create a Telegram bot

In Telegram, open `@BotFather`, create a dedicated bot, and copy its token.

Copy `.env.example` to `.env`:

```bash
cp .env.example .env
```

Edit:

```text
TELEGRAM_BOT_TOKEN=123456:ABC...
```

If you want to test without Telegram, leave the token empty. The Streamlit
manual input and "Demo data" button still work.

## 2. Install

Recommended Python: 3.10+

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

## 3. Test llama.cpp

From the machine that runs Streamlit:

```bash
curl http://100.110.236.59:8088/health
```

The llama.cpp server must be listening on a network-reachable interface, for
example:

```bash
./llama-server \
  -m /path/to/model.gguf \
  --host 0.0.0.0 \
  --port 8088
```

## 4. Run

```bash
streamlit run app.py
```

Open the Streamlit URL shown in the terminal, normally:

```text
http://localhost:8501
```

## 5. Auditorium flow

1. Presenter shows the challenge.
2. Audience scans the Telegram QR.
3. Audience sends needs, experiences, constraints, criticisms, and ideas.
4. Live Feed shows contributions using pseudonymous IDs.
5. Presenter presses **ANALYZE COLLECTIVE INTELLIGENCE**.
6. llama.cpp generates Q1, Q2, Q3, Q4.
7. TISE shows disagreements and ethics/guardrails.
8. TISE asks a new question and proposes three vote options.
9. Audience responds again: the cycle continues.

## Useful demo safeguards

- Use a dedicated Telegram bot.
- Do not ask the audience to submit private/sensitive data.
- The app pseudonymizes Telegram participant IDs before display.
- Audience text is explicitly treated as untrusted DATA in the LLM system prompt.
- Keep the manual Streamlit input and "Demo data" button as fallback.
