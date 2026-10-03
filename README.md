# FitBuddy - AI Fitness Plan Generator

FastAPI + Google Gemini + SQLAlchemy/SQLite + Jinja2.
Generates a personalized 7-day workout plan and a nutrition/recovery tip, lets users refine the plan
with feedback, and gives admins a dashboard of all users and plans.

## Quick start

```bash
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env              # then paste your GOOGLE_API_KEY into .env
uvicorn app.main:app --reload
```

Open http://127.0.0.1:8000 (app) and http://127.0.0.1:8000/docs (API docs).

No API key yet? The app still runs and returns clearly labelled offline sample plans.

## Structure

```
app/
  main.py                   app setup, static files, DB init
  routes.py                 /, /generate-workout, /submit-feedback, /view-all-users, /delete-user/{id}
  gemini_client.py          shared Gemini client, model names, offline fallback
  gemini_generator.py       generate_workout_gemini()            (Pro model)
  gemini_flash_generator.py generate_nutrition_tip_with_flash()  (Flash model)
  updated_plan.py           update_workout_plan()                (Pro model)
  database.py               SQLAlchemy models + save_user/save_plan/update_plan/get_* helpers
  schemas.py                Pydantic: UserInput, FeedbackRequest
  templates/                index.html, result.html, all_users.html
  static/                   style.css, images/ (optional gym.jpg background)
```

## Notes

- Models: `gemini-1.5-pro` from the original write-up has been retired by Google, so the defaults are
  `gemini-2.5-pro` / `gemini-2.5-flash`. Override with `GEMINI_PRO_MODEL` / `GEMINI_FLASH_MODEL` in `.env`.
- SDK: uses the current `google-genai` package (the older `google-generativeai` is deprecated).
- Original and updated plans are stored in separate columns; the admin page shows both.
- Set `ADMIN_KEY` in `.env` to protect the admin page (`/view-all-users?key=...`).
- Add `app/static/images/gym.jpg` for a photo background (a dark gradient is used otherwise).
- Re-submitting an existing User ID overwrites that user's details and plan.
- Plans are general fitness information, not medical advice (disclaimers are in the UI and prompts).
