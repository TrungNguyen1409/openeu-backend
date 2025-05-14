# ProjectEurope - OpenEU - Backend

<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->

[![All Contributors](https://img.shields.io/badge/all_contributors-16-orange.svg?style=flat-square)](#contributors-)

<!-- ALL-CONTRIBUTORS-BADGE:END -->

## 📘 Swagger UI & OpenAPI Docs

You can test and explore the API via:

- 🧪 Swagger UI: [http://localhost:8000/docs](http://localhost:8000/docs)
- 📕 ReDoc: [http://localhost:8000/redoc](http://localhost:8000/redoc)
- 🧾 OpenAPI JSON: [http://localhost:8000/openapi.json](http://localhost:8000/openapi.json)

## 📦 Project Structure

```
openeu-backend/
├── app/
│   ├── api/
│   │   └── meetings.py              # REST API endpoints using FastAPI
│   ├── core/
│   │   ├── config.py              # Loads environment variables, has a Settings class that contains all env vars we need
│   │   └── supabase_client.py     # Initializes Supabase client
│   ├── data_sources/
│   │   ├── scrapers/
│   │   │   └── site1-scraper.py           # Scraper for website 1
│   │   ├── apis/
│   │   │   └── source1_api.py     # Fetcher for API-based source 1
│   │   └── task_runner.py        # Aggregates all sources (scrapers + APIs)
├── scripts/
│   └── run_job.py               # script for running task_runner
├── supabase/
│   ├── schemas/
│   │   ├── meetings.sql
├── main.py                        # FastAPI application entrypoint
├── .gitignore
├── .env
├── pyproject.toml                  # Poetry configuration file
└── README.md
```

## Installing dependencies
This project uses Poetry for dependency management and packaging. So in order to install all dependencies, you need to install Poetry first. To do so, follow these steps:
- Install pipx on your computer as described [here](https://pipx.pypa.io/stable/installation/)
- Install Poetry by running ```pipx install poetry```

After that, you can install the dependencies of this project:
```
poetry install
```

This command creates a virtual environment (if one doesn’t exist) and installs all dependencies defined in pyproject.toml. 
To run the project, run 
```
poetry run uvicorn main:app
```

In order to activate pre commit hooks, you need to run the following command:
```
pre-commit install
```

If you want to install dependencies from `requirements.txt`, you can generate it using:
```
poetry export --without-hashes --format=requirements.txt --output requirements.txt
```

## Database Schemas
The database schema is defined inside this project and must be pushed to Supabase once 
- First, install the Supabase CLI as described [here](https://supabase.com/docs/guides/local-development/cli/getting-started#installing-the-supabase-cli)
- Start Docker on your computer
- Generate a migration file by calling ```supabase db diff -f <MIGRATION_NAME>```
- Stop all local instances of Supabase and then apply the migration by calling ```supabase start && supabase migration up```
- Login to Supabase by calling ```supabase login```
- Link the local project to the remote database by calling ```supabase link```
- Push the changes to the remote Supabase instance by calling ```supabase db push```

## Testing the Crawler endpoint
In order to test the `crawler` endpoint you need to do the following: 
 - First assign `CRAWLER_API_KEY` an arbitrary string in the `.env` file
 - Prepare a `GET` request in [Postman](https://www.postman.com/downloads/) to `localhost:8000/crawler/`
 - Navigate in Postman to the Headers Tab
 - Add a new header 
   - Key: `token` 
   - Value: The same value you set for `CRAWLER_API_KEY` in your `.env` file 
 - Send the request

## Backend Deployment
The deployment of our backend application is fully automated using GitHub Actions.
Each time a commit is pushed to the main branch, a deployment workflow is triggered to ensure that the latest changes are live without manual intervention.

The application is hosted on Render, and the latest version of the backend can be accessed at: ```https://openeu-backend.onrender.com```

### Environment Variables
Please ensure that you added all necessary environment variables to production before pushing to main.
You can add the environment variables for production [here](https://dashboard.render.com/web/srv-d0fpj1idbo4c73ankui0/env).

## Background Jobs
In our project, each background job is associated with a dedicated API endpoint. 
When the corresponding endpoint is triggered, the background job is executed.
We use [cron-job](https://console.cron-job.org/) to automate this process, which invokes these endpoints at predefined intervals.

If you add a new background job, please make sure to: 
- protect the endpoint using the `get_token_header` function from [dependencies.py](./app/dependencies.py) (an example can be found [here](./app/api/crawler.py))
- after adding the cronjob, also add it to the status page [here](https://console.cron-job.org/statuspages/26586)
