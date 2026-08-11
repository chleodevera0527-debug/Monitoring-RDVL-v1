# R.DEVERA Truck Monitor - Render Deployment

## Repository layout

Keep these files in the GitHub repository root:

- app.py
- requirements.txt
- render.yaml
- .python-version
- .env.example
- .gitignore
- .renderignore
- migrate_sqlite_to_postgres.py

## Render deployment

The included `render.yaml` provisions:

- R.DEVERA web service
- Render Postgres database
- DATABASE_URL connection from the database to the web service
- generated APP_SECRET
- health check at `/health`

The production web service uses Gunicorn.

Build command:

`python -m pip install --upgrade pip && python -m pip install -r requirements.txt`

Start command:

`python -m gunicorn app:app --bind 0.0.0.0:$PORT --workers 2 --threads 4 --timeout 120`

## Required production secrets

Set these in Render Environment:

- ADMIN_USERNAME=admin
- ADMIN_PASSWORD=your-private-password
- ADMIN_PASSWORD_RESET=0
- ALLOW_DEFAULT_ADMIN=0
- COOKIE_SECURE=1

`DATABASE_URL` and `APP_SECRET` are provisioned by the Blueprint.

Never commit production passwords, database URLs, or `.env` files.

## Local Visual Studio use

When `DATABASE_URL` is not set, the application uses SQLite:

`truck_monitor.db`

For local development, use:

- ADMIN_USERNAME=admin
- ADMIN_PASSWORD=your-private-password
- ALLOW_DEFAULT_ADMIN=0
- COOKIE_SECURE=0

The application creates the configured admin account when `ADMIN_PASSWORD` is supplied.

## Admin password recovery

To deliberately reset the configured admin password:

1. Set `ADMIN_PASSWORD` to the new password.
2. Set `ADMIN_PASSWORD_RESET=1`.
3. Deploy once.
4. Log in with the new password.
5. Set `ADMIN_PASSWORD_RESET=0`.
6. Deploy again.

Do not leave password reset enabled.

## SQLite to PostgreSQL migration

For an existing SQLite database:

`DATABASE_URL="postgresql://..." python migrate_sqlite_to_postgres.py truck_monitor.db`

The target PostgreSQL database must be initialized by the application before migration.

## Important Render note

Free Render Web Services spin down after 15 minutes without inbound traffic, and their local filesystem is ephemeral. This application therefore uses Render Postgres as the production source of truth instead of relying on the local SQLite file.

Free Render Postgres databases expire after 30 days. For real production logistics data, use a paid Render Postgres instance or upgrade the database before the free database expires.

## Updating GitHub

Replace the old deployment files with this bundle, commit, and push.

After the push, verify that Render is using the repository root as the Root Directory when `app.py` is at the repository root.

Do not keep an older `requirements.txt` containing PyInstaller dependencies.

The production requirements are only:

- gunicorn
- psycopg2-binary
