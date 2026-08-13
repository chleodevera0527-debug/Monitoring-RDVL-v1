# R.DEVERA Truck Monitor - Render Persistent Database Version

Production uses Render PostgreSQL through DATABASE_URL. Do not rely on SQLite on Render because the web-service filesystem is ephemeral.

## Render
1. Push all files in this folder to the GitHub repository root.
2. Create/use the Render PostgreSQL database and connect DATABASE_URL to the web service.
3. Set REQUIRE_POSTGRES=1 on the Render web service.
4. Set APP_SECRET to a long random secret.
5. Set ADMIN_USERNAME and ADMIN_PASSWORD as needed.
6. Deploy.

The application initializes the PostgreSQL schema automatically.

## Existing SQLite records
If your current records are in truck_monitor.db, migrate them before switching production permanently:

DATABASE_URL="postgresql://..." python migrate_sqlite_to_postgres.py truck_monitor.db

The SQLite source is read-only during migration and is not deleted.

## Important
Render Web Service restarts/sleeps do not erase PostgreSQL data. The PostgreSQL database is separate from the web service filesystem. Database retention depends on the Render PostgreSQL plan; keep a paid persistent database for production data.
