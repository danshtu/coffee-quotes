# Coffee Quotes System

A full-stack application with Django REST API backend, React frontend, and PostgreSQL database.

## Tech Stack

- **Backend:** Python 3.13 + Django 5.2.7 + Django REST Framework 3.15.2
- **Frontend:** Node 20 + React 19 + React Router 7
- **Database:** PostgreSQL 18

## Prerequisites

- Docker
- Docker Compose

## Docker Compose Structure

This project uses a multi-file Docker Compose setup:

- **`docker-compose.yml`** - Base configuration (shared by all environments)
- **`docker-compose.dev.yml`** - Development environment overrides
- **`docker-compose.prod.yml`** - Production environment overrides

## Getting Started

### Development Environment

#### 1. Clone the repository

```bash
git clone <repository-url>
cd coffee-quotes
```

#### 2. Set up environment variables

Copy the example environment file and customize it:

```bash
cp .env.example .env
```

Edit `.env` and update the following values:
- `DJANGO_SECRET_KEY`: Generate a new secure key
- `POSTGRES_PASSWORD`: Set a strong password

#### 3. Start the application (Development)

Build and start all services with development configuration:

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up -d --build
```

This command will:
- Start PostgreSQL 18 database on port 5432
- Start Django backend on port 8080 with hot reloading
- Start React frontend on port 5173 with hot reloading (Vite dev server)

#### 4. Run database migrations

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml exec backend python manage.py migrate
```

#### 5. Create a superuser (optional)

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml exec backend python manage.py createsuperuser
```

### Production Environment

#### 1. Set up production environment variables

Copy the production example file:

```bash
cp .env.prod.example .env.prod
```

Edit `.env.prod` with production values:
- `DJANGO_SECRET_KEY`: Generate a strong 50+ character key
- `POSTGRES_PASSWORD`: Use a strong, unique password
- `ALLOWED_HOSTS`: Your production domain(s)
- `CORS_ALLOWED_ORIGINS`: Your frontend production URL(s)
- Set `DEBUG=False`

#### 2. Start the application (Production)

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod up -d --build
```

This will:
- Run Django with Gunicorn (4 workers, 2 threads)
- Build optimized production containers
- Disable debug mode
- Not expose database port externally

#### 3. Run migrations (Production)

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod exec backend python manage.py migrate
```

## Docker Commands

All commands require specifying the compose files. For convenience, you can create shell aliases:

```bash
# Add to your ~/.bashrc or ~/.zshrc
alias dc-dev='docker-compose -f docker-compose.yml -f docker-compose.dev.yml'
alias dc-prod='docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod'
```

### Development Commands

#### View logs

```bash
# All services
docker-compose -f docker-compose.yml -f docker-compose.dev.yml logs -f

# Specific service
docker-compose -f docker-compose.yml -f docker-compose.dev.yml logs -f backend
docker-compose -f docker-compose.yml -f docker-compose.dev.yml logs -f frontend
docker-compose -f docker-compose.yml -f docker-compose.dev.yml logs -f db
```

#### Stop the application

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml down
```

#### Stop and remove all data (including database)

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml down -v
```

#### Rebuild containers

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up -d --build
```

#### Access Django shell

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml exec backend python manage.py shell
```

#### Access PostgreSQL database

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml exec db psql -U postgres -d coffee_quotes
```

#### Run Django management commands

```bash
docker-compose -f docker-compose.yml -f docker-compose.dev.yml exec backend python manage.py <command>
```

### Production Commands

#### View logs

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod logs -f
```

#### Stop the application

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod down
```

#### Restart a service

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod restart backend
```

#### Run Django management commands

```bash
docker-compose -f docker-compose.yml -f docker-compose.prod.yml --env-file .env.prod exec backend python manage.py <command>
```

## Development vs Production

### Development Mode Features:
- Hot reloading enabled for both frontend and backend
- Debug mode enabled (`DEBUG=True`)
- Volume mounts for live code updates
- Django development server (`runserver`)
- Vite dev server for frontend on port 5173
- Database port exposed for local access (5432)
- Verbose error messages

### Production Mode Features:
- Optimized builds
- Debug mode disabled (`DEBUG=False`)
- Gunicorn WSGI server (4 workers, 2 threads) for backend
- React Router production server for frontend on port 3000
- No volume mounts (code baked into images)
- Database not exposed externally
- Non-root user for security
- Automatic container restarts (`restart: unless-stopped`)

## Accessing the Application

### Development:
- **Frontend:** http://localhost:5173 (Vite dev server)
- **Backend API:** http://localhost:8080
- **Django Admin:** http://localhost:8080/admin

### Production:
- **Frontend:** http://localhost:3000 (React Router production server)
- **Backend API:** http://localhost:8080
- **Django Admin:** http://localhost:8080/admin

## Project Structure

```
coffee-quotes/
├── backend/                    # Django application
│   ├── core/                   # Django project settings
│   ├── quotes/                 # Quotes app
│   └── Dockerfile              # Multi-stage: development & production
├── frontend/                   # React application
│   └── Dockerfile
├── docker-compose.yml          # Base configuration (shared)
├── docker-compose.dev.yml      # Development overrides
├── docker-compose.prod.yml     # Production overrides
├── .env                        # Development environment variables (not in git)
├── .env.example                # Development environment template
├── .env.prod                   # Production environment variables (not in git)
└── .env.prod.example           # Production environment template
```

## Environment Variables

- **Development:** See `.env.example` for all available development environment variables
- **Production:** See `.env.prod.example` for production-specific configurations

## Security Notes

- Never commit `.env` or `.env.prod` files to version control
- Always use strong, unique passwords for production
- Generate a new Django secret key for production (50+ characters)
- Keep `DEBUG=False` in production
- Configure `ALLOWED_HOSTS` with your actual domain(s)
- Use HTTPS for CORS origins in production
