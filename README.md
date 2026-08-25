# IntelliEXpense

A personal expense tracker built with Django that automatically categorises spending from free-text descriptions and forecasts future expenses from historical patterns.

Rather than asking users to pick a category from a dropdown every time, IntelliEXpense infers it from what they type — "flat white at Karma Coffee" becomes *Food & Drink* without any manual tagging. A time-series model then projects forward from past spending to support budgeting.

---

## Features

**Automatic expense categorisation** — Descriptions are tokenised and normalised with NLTK, then classified into spending categories. Users can override the prediction when it gets one wrong.

**Expense forecasting** — An ARIMA model fitted on historical transactions projects future spending, giving users a forward view rather than only a record of the past.

**Goal tracking** — Users set savings or spending targets and track progress against them.

**Report generation** — Spending summaries exported as PDF, with charts rendered via matplotlib.

**Multi-currency support** — Currency preferences per user, backed by a bundled currency reference.

**REST API** — Django REST Framework endpoints for programmatic access to expense data.

**Account management** — Registration, login, email verification, and password reset.

---

## Tech stack

| Layer | Technology |
|---|---|
| Framework | Django 5.1 |
| Language | Python 3.12 |
| Database | SQLite |
| ML / NLP | scikit-learn, NLTK |
| Forecasting | statsmodels (ARIMA) |
| API | Django REST Framework |
| Async tasks | Celery + Redis |
| Reporting | matplotlib, xhtml2pdf |

---

## Getting started

### Prerequisites

- Python 3.12
- Redis (only needed for background tasks)

### Installation

Clone and enter the project:

```bash
git clone https://github.com/KingaTshering10/IntelliEXpense-repo.git
cd IntelliEXpense-repo
```

Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Download the NLTK corpora used for text preprocessing:

```bash
python -c "import nltk; nltk.download('punkt_tab'); nltk.download('stopwords'); nltk.download('wordnet')"
```

### Configuration

Copy the example environment file and fill in your own values:

```bash
cp .env.example .env
```

Generate a Django secret key:

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Paste it into `.env` as `SECRET_KEY`. Email features additionally require a Gmail app password in `EMAIL_HOST_PASSWORD` — a regular account password will not work.

### Running

Apply migrations and start the server:

```bash
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

The app is then available at http://127.0.0.1:8000/.

To run background tasks, start a Celery worker in a second terminal:

```bash
celery -A expensetracker worker --loglevel=info
```

---

## Project structure

```
expensetracker/      Project settings, URLs, Celery config
expenses/            Expense records and ML categorisation
expense_forecast/    ARIMA forecasting
userincome/          Income tracking
goals/               Savings and spending goals
report_generation/   PDF report export
authentication/      Registration, login, password reset
userprofile/         User profile management
userpreferences/     Currency and display settings
api/                 REST Framework endpoints
templates/           HTML templates
static/              CSS, JS, images
dataset.csv          Training data for the categoriser
```

---

## Notes and limitations

The categorisation model is trained on `dataset.csv`, which is small and skewed toward common Western spending categories — accuracy on unusual or region-specific merchants is limited.

ARIMA forecasting assumes reasonably regular spending history. Results on sparse or highly irregular data are unreliable.

Model training currently runs at application startup rather than being cached to disk, which slows the first request after a restart.

SQLite is used for simplicity and is not suited to concurrent multi-user deployment.

---

## Licence

MIT
