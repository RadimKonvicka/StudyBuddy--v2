# StudyBuddy – Souhvězdí znalostí

> **Maturitní projekt** · Radim Konvička · SŠPU Opava

Webová aplikace, která z tématu nebo nahraných materiálů (PDF, DOCX, TXT) vytvoří **personalizovaný studijní plán**.
Tým AI agentů připraví vstupní test, zjistí slabá podtémata a adaptivně na ně zaměří výklad, kartičky i testy.

## Technologie

| Část | Technologie |
|---|---|
| Frontend | Astro (statický web) + Tailwind CSS 4, LiaScript export |
| Backend | Python, FastAPI, SQLAlchemy 2 |
| AI | Google Gemini API (`gemini-2.5-flash`), výstupy jako validovaný JSON |
| Dokumenty | pypdf, python-docx |
| Databáze | PostgreSQL (Supabase) v produkci, SQLite lokálně |
| Přihlašování | Argon2 hash hesel, JWT tokeny |
| Nasazení | Vercel (frontend) + Render (backend) |

## Struktura

```
backend/
  app/
    main.py        vstupní bod FastAPI
    config.py      nastavení z .env
    database.py    připojení k DB
    models.py      databázové tabulky
    schemas.py     tvar dat API (Pydantic)
    security.py    hashování hesel, JWT
    deps.py        ověření přihlášeného uživatele
    routers/       HTTP endpointy
    services/      logika bez AI
    agents/        AI agenti
  tests/           pytest
frontend/
  src/pages/       stránky
  src/components/  komponenty
  src/lib/         API klient, přihlášení, pomocné funkce
  src/styles/      design systém
```

## Spuštění lokálně (Windows, PowerShell)

Potřebuješ **Python 3.12+** a **Node.js 22.23+**.

### Backend

```powershell
cd backend
py -3 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
Copy-Item .env.example .env      # pak v .env doplň GEMINI_API_KEY a SECRET_KEY
uvicorn app.main:app --reload
```

`SECRET_KEY` vygeneruješ příkazem `python -c "import secrets; print(secrets.token_urlsafe(48))"`.
API běží na http://localhost:8000, dokumentace na http://localhost:8000/docs.

Pokud PowerShell nedovolí aktivovat prostředí: `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`.

### Frontend

```powershell
cd frontend
npm install
npm run dev
```

Aplikace běží na http://localhost:4321.

### Testy

```powershell
cd backend
.\.venv\Scripts\python.exe -m pytest
```

## Bezpečnost

- Hesla se ukládají jen jako hash Argon2id, přihlášení přes podepsaný JWT.
- Všechna data jsou oddělená po uživatelích, cizí záznam vrátí 404.
- Žádné klíče v kódu – vše v `.env` (není v Gitu).
- Ochrana proti hádání hesel (omezení počtu pokusů).
