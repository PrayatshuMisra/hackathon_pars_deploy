# Deploying PARS to Railway

This guide outlines the steps to deploy the PARS application (Backend and Frontend) to [Railway](https://railway.app/).

## Prerequisites

1.  A Railway account.
2.  Your project code pushed to a GitHub repository.

## Deployment Steps

### Step 1: Create a Railway Project

1.  Go to your Railway Dashboard.
2.  Click **New Project** -> **Deploy from GitHub repo**.
3.  Select your repository (`hackathon_pars`).

### Step 2: Configure the Backend Service

Railway will likely detect the repository and try to deploy. First, let's make sure the backend is set up correctly.

1.  In your project view, click on the service card created for your repo.
2.  Go to **Settings**.
3.  **Root Directory**: Change this to `/backend`. This tells Railway to look for the `Dockerfile` inside the `backend` folder.
4.  **Networking**: 
    -   Click on the **Networking** tab (Wait for the service to build or try to build first).
    -   Railway will ask for a **Target Port**. Enter **8000**.
    -   Click **Generate Domain**. You will get a URL like `pars-backend-production.up.railway.app`. Note this URL.
5.  **Variables**: Add any environment variables your backend needs (e.g., `VITE_GEMINI_API_KEY` if used on backend).
6.  The service should redeploy automatically. Check the **Deployments** tab to ensure it builds successfully.

### Step 3: Add the Frontend Service

Now we need to add the frontend as a second service in the same project.

1.  Click **+ New** -> **GitHub Repo** -> Select the same repository again.
2.  This creates a second service card. Click on it.
3.  Go to **Settings**.
4.  **Root Directory**: Leave this as `/` (root), since the frontend `Dockerfile` is in the root.
5.  **Variables**: 
    - Add a variable named `FASTAPI_URL`.
    - Set the value to the **full URL** of your backend service from Step 2 (e.g., `https://pars-backend-production.up.railway.app`).
    - **Crucial**: Railway builds Docker images. The `ARG FASTAPI_URL` in the Dockerfile needs to be populated. In Railway, you might need to specify this as a **Build Argument** (if available) or as a regular environment variable. Railway typically injects variables during build.
6.  **Networking**: 
    -   Click the **Networking** tab.
    -   **Target Port**: Enter **80**. (Nginx default).
    -   Click **Generate Domain**. You will get a URL like `pars-frontend.up.railway.app`.
7.  The service should redeploy.

### Step 4: Verify Deployment

1.  Open your frontend URL.
2.  Check the browser console/network tab to confirm that API requests are going to your backend URL (`https://pars-backend-production.up.railway.app/predict`) and not `localhost`.

## Troubleshooting

-   **Backend 404/Connection Refused**: Ensure the backend service has a public domain generated in Railway settings.
-   **Frontend API Errors**: Check the `FASTAPI_URL` environment variable. If it's undefined in the build, the frontend might be defaulting to `http://localhost:8000`. You may need to trigger a **Redeploy** on the frontend service after setting the variable.
