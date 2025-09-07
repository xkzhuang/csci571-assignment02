# CSCI571 Assignment 02

Deployment:
 - Deploying with Cloud Run or App Engine?
 - Cloud Run (best option for Flask backends)
   - How it works: Package your Flask app into a container → Cloud Run runs it on demand.
   - Billing: per request + CPU/memory per second.
   - Idle cost: $0 → it scales to zero.
   - Cheapest option if your backend won’t have constant traffic.
   - Works great with Flask, FastAPI, Django, etc.
 
 - App Engine Standard
   - Keeps 1 instance warm 24/7, even with no traffic.
   - Costs ~$1.50–$2/month minimum.
   - More expensive than Cloud Run/Functions if you’re just testing.


Step-by-step: Deploy Flask to Cloud Run:

 - my-flask-app/
    ├── app.py
    ├── requirements.txt
    └── Dockerfile

 - Dockerfile:
   FROM python:3.11-slim
   
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install --no-cache-dir -r requirements.txt
   
   COPY app.py .
   
   CMD ["gunicorn", "-b", "0.0.0.0:8080", "app:app"]

 - Deploy:
   gcloud config set project <YOUR_PROJECT_ID>
   gcloud services enable run.googleapis.com

   gcloud run deploy flask-backend \
       --source . \
       --region us-central1 \
       --allow-unauthenticated


 - Test:
   https://flask-backend-xxxxxx-uc.a.run.app


Step-by-step: Deploy Flash using App Engine:

 - my-flask-app/
    ├── app.py
    ├── requirements.txt
    └── app.yaml

 - app.yaml
   runtime: python313
   handlers:
   - url: /static
     static_dir: static
   - url: /.*
     script: auto


 - Deploy:
   gcloud services enable cloudbuild.googleapis.com
   gcloud services enable appengine.googleapis.com
   gcloud services enable storage-component.googleapis.com
   
   gcloud run deploy <project_name_or_name_of_the_service> --region=us-west1
   gcloud run deploy <project_name_or_name_of_the_service> --region=us-west1 --source .


 - Test:
   gcloud projects list ---> list project with project ID, Number

   https://[PROJECT_ID]-[PROJECT_NUMBER].[REGION_ID_NAME].run.app
   https://csci571-assignment02-506083499014.us-west1.run.app



 - Check and stop:
   gcloud run services list --region=us-west1
   gcloud run services delete <SERVICE_NAME> --region=us-west1

