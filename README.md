# CLBB-CityScope

CLBB-CityScope is a pipeline for updating cityscope models, originally developed by the CityScience lab in BioBio.

## Project Overview

This project consists of several interconnected repositories:
- **clbb-io**: Backend API and core functionality
- **clbb-remote-controller**: Remote control interface for managing map layers and states
- **clbb-front**: Frontend dashboard
- **clbb-projection**: Projection visualization system

## Quick Start Guide

### Prerequisites

- Docker and Docker Compose
- Git

### Step 1: Clone this Repository

```bash
git clone https://github.com/your-username/clbb-cityscope.git
cd clbb-cityscope
```

### Step 2: Create and Configure .env File

Create a file named `.env` in the project root directory with the following content:

```
DB_CONTAINER_NAME=core_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=clbb_db
DATABASE_URL=postgres://postgres:your_secure_password@db:5432/clbb_db
API_PORT=9900
FRONT_PORT=80
```

**Important:** 
- Replace `your_secure_password` with a strong password
- Use the same password in both the `POSTGRES_PASSWORD` and `DATABASE_URL` lines
- Don't use quotes around values

Example with a password:
```
POSTGRES_PASSWORD=r3@llyStr0ngP@ssw0rd
DATABASE_URL=postgres://postgres:r3@llyStr0ngP@ssw0rd@db:5432/clbb_db
```

### Step 3: Start the Services

Start all services with Docker Compose:

```bash
docker-compose up -d
```

This will:
1. Build and start all containers
2. Run the initialization script inside the container which:
   - Waits for the database to be ready
   - Runs database migrations
   - Checks for first-time initialization (using a flag file in `/app/data/db_initialized`)
   - If first-time:
     - Cleans up any existing data
     - Creates sample data
     - Creates a default admin user
   - Starts the Django server

To check if all services are running:
```bash
docker-compose ps
```

To view logs:
```bash
docker-compose logs -f
```

### Step 4: Access the Application

Once everything is running, you should be able to access different parts of the application:

1. **Main Dashboard** (http://localhost/dashboard/):
   - Shows key metrics and charts
   - Displays population density, green space coverage, and building height data
   - Updates in real-time with the latest data

2. **Projection Interface** (http://localhost/projection/):
   - Interactive map visualization
   - Shows geographic data and spatial analysis
   - Features:
     - Multiple layer support
     - Layer opacity controls
     - Geographic data visualization
     - Real-time updates
     - Custom layer configurations

3. **Remote Controller** (http://localhost/remote/):
   - Control interface for managing map layers and states
   - Features:
     - Layer selection buttons for different indicators
     - State control buttons (P-1 through P-7) for toggling different states
     - Reset buttons for quick state management:
       - ACTUAL: Resets all states to 0
       - FUTURO: Sets all states to 1
     - Real-time state updates
     - Visual feedback with glowing/neon button effects
     - Start/Stop controls for the projection system

4. **Admin Interface** (http://localhost:9900/admin):
   - Manage indicators, states, and configurations
   - Create and modify users
   - View and edit all data

5. **API Documentation** (http://localhost:9900/api/):
   - REST API endpoints documentation
   - Test API endpoints directly
   - View available data structures

### Step 5: Default Admin User

A default admin user is created during setup with the following credentials:
- Username: admin
- Password: admin123

**Important**: Please change the default password after first login!

To change the admin password:
1. Log in to the admin interface
2. Go to the Users section
3. Click on the admin user
4. Change the password

## Data Management

### Data Persistence

The application uses Docker volumes to persist data:
- `postgres_data_core`: Stores the database data
- `core_api_data`: Stores application-specific data (like initialization flags)
- `dashboard_build`: Stores the frontend build files

This means that:
1. All your data (indicators, states, configurations) persists between container restarts
2. Sample data is only created on first initialization
3. Any changes made through the admin interface or API are preserved

### Managing Data

You can manage your data through:
1. **Admin Interface** (http://localhost:9900/admin)
   - Manage indicators, states, and configurations
   - Create and modify users
   - View and edit all data

2. **Remote Controller** (http://localhost/remote/)
   - Control map layers and states in real-time
   - Toggle between different indicators
   - Manage projection states
   - Start/Stop the projection system

3. **API Endpoints** (http://localhost:9900/api/)
   - Programmatically interact with the data
   - Create, read, update, and delete records
   - See [API_DOCUMENTATION.md](API_DOCUMENTATION.md) for details

### Starting Fresh

If you need to start fresh with a clean database:
```bash
# Stop all containers and remove volumes
docker-compose down -v

# Start everything up again
docker-compose up -d
```

**Warning**: This will delete all data and recreate the sample data.

### Regular Operations

For normal operation:
```bash
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# Restart a specific service
docker-compose restart web

# View logs
docker-compose logs -f

# Rebuild containers (if you've made changes)
docker-compose up -d --build
```

## Documentation

For detailed documentation about:
- API endpoints and usage
- Sample data structure
- Database management
- API features and examples

Please refer to [API_DOCUMENTATION.md](API_DOCUMENTATION.md)