# CIS566_FInal_project
## Hotel Reservation System - Full Stack Deployment Guide

This repository documentation covers the deployment and execution of the complete Hotel Reservation System, consisting of a **FastAPI Backend** (handling business logic and database interactions) and a **Vanilla JS/HTML Frontend** (served via Nginx).

## System Architecture

| Component | Technology | Description |
| :--- | :--- | :--- |
| **Frontend** | HTML5, CSS3, JavaScript | Client-side UI using standard web technologies. Served via Nginx in Docker. |
| **Backend** | Python (FastAPI) | RESTful API implementing Layered Architecture and RBAC. |
| **Database** | Supabase (PostgreSQL) | Cloud database for persistence, auth, and stored procedures. |
| **Deployment** | Docker, Render | Containerized frontend and cloud-hosted backend. |

-----

## Prerequisites

Before starting, ensure you have the following installed/configured:

  * **Docker Desktop** (for running the frontend container)
  * **Python 3.11+** (for running the backend)
  * **Git**
  * **Supabase Account** (for the database)

-----

## Part 1: Backend Setup (API & Database)

It is recommended to start the backend first so the API is available for the frontend.

### 1\. Clone the Repository

```bash
git clone https://github.com/dkshitij29/cis566-Software_Architecture_and_Design_Patterns.git
cd cis566-Software_Architecture_and_Design_Patterns/backend
```

### 2\. Configure the Database (Supabase)

1.  Log in to your [Supabase Dashboard](https://supabase.com/).
2.  Create a new project.
3.  Navigate to the **SQL Editor** in Supabase.
4.  Execute the schema scripts found in the `database/db.config` file of this repo to create necessary tables (`users`, `rooms`, `bookings`) and RPC functions (`process_payment_and_confirm`).

### 3\. Environment Variables

Create a `.env` file inside the `backend/` directory and populate it with your Supabase credentials:

```ini
supabase_url="YOUR_SUPABASE_PROJECT_URL"
supabase_key="YOUR_SUPABASE_ANON_KEY"
SECRET_KEY="YOUR_GENERATED_SECRET_KEY"
```

### 4\. Install Dependencies & Run

Set up the virtual environment and start the server:

```bash
# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install requirements
pip install -r requirements.txt

# Start the API server
fastapi dev main.py
# OR
uvicorn main:app --reload
```

> **Status:** The Backend API is now running at `http://127.0.0.1:8000`.
> You can view the API documentation at `http://127.0.0.1:8000/docs`.

-----

## 💻 Part 2: Frontend Setup (User Interface)

### 1\. Clone the Repository

Open a new terminal window (keep the backend running) and clone the frontend:

```bash
git clone https://github.com/dicksor112801/frontend-cis566.git      
cd frontend-cis566
```

### 2\. API Configuration (Important)

*Ensure your frontend JavaScript files point to the correct Backend API URL.*
If you are running locally, ensure the fetch requests in `functionality/*.js` are targeting `http://127.0.0.1:8000`.

### 3\. Build & Run with Docker

We use Docker to build a lightweight Nginx container to serve the static files.

```bash
# 1. Build the Docker image
docker build -t hotel-frontend .

# 2. Run the container on port 8080
docker run -p 8080:80 hotel-frontend
```

> **Status:** The Frontend is now accessible at `http://localhost:8080/index.html`.

-----

## Production Deployment

### Backend (Render)

The backend includes a `render.yaml` file for automated deployment on [Render.com](https://render.com).

1.  Connect your GitHub repo to Render.
2.  Select "Web Service."
3.  Add the Environment Variables (`supabase_url`, etc.) in the Render dashboard.
4.  Render will use the configuration to build and deploy the Python service.

### Frontend

Since the frontend is Dockerized:

1.  You can deploy the Docker image to any container registry (Docker Hub, AWS ECR).
2.  Host it on platforms like **Render**, **AWS ECS**, or **DigitalOcean App Platform**.
3.  **Note:** When deploying to production, update the API URL in your JavaScript files to point to your *production* backend URL (e.g., `https://your-api.onrender.com`), not localhost.

-----

## Troubleshooting

| Issue | Solution |
| :--- | :--- |
| **CORS Errors** | Ensure the Backend `main.py` has CORS middleware configured to allow requests from `http://localhost:8080`. |
| **Database Connection Fail** | Double-check your `supabase_url` and `supabase_key` in the `.env` file. |
| **Frontend 404** | Make sure you are accessing the full path `http://localhost:8080/index.html` if `index.html` is not set as root. |
