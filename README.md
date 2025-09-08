# CSCI571 Assignment 02

### Setup Google Cloud
 - Create a Google Cloud Account @ `https://console.cloud.google.com/`
 - Install Google Cloud SDK (gcloud CLI)
   - https://cloud.google.com/sdk?hl=en (click on Install SDK)
   - And check guide: `Installing the latest gcloud CLI version (537.0.0)`
     ```
     sudo apt-get update
     sudo apt-get install apt-transport-https ca-certificates gnupg curl
     curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
     echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
     sudo apt-get update && sudo apt-get install google-cloud-cli
     ```
 - Setup python for gcloud
   ```
   sudo apt update
   sudo apt install python3 python3-pip
   pip install google-cloud-storage
   ```
   if package isolation is needed:
   ```
   mkdir <proj_folder>
   cd <proj_folder>
   
   sudo apt update
   sudo apt install python3-dev python3-venv 
   python3 -m venv env
   source env/bin/activate
   
   pip install google-cloud-storage
   ```
   To exit the venv and return to the system python
   ```
   deactivate
   ```


## Deployment:
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


## Step-by-step: Deploy Flash using App Engine:
 - gcloud init
   - create new configuration
   - choose gcloud account
   - creaet new google cloud project

 - gcloud app create --region=us-west1

 - Build the web app:
```
  my-flask-app/
    ├── app.py
    ├── requirements.txt
    └── app.yaml
```
```
 app.yaml
   runtime: python313
   handlers:
   `
   - url: /static
     static_dir: static
   - url: /.*
     script: auto `
 ``` 

 - ### Deploy (Each deploy will introduce a version):
   - `gcloud app deploy`
     - If this failed, check the bucket permissions:
       - `gsutil ls`
       - `gsutil iam get gs://<PROJECT_ID>.appspot.com`   --- check permission of project bucket
       - Make sure it has `roles/storage.admin` or at least `objectAdmin/objectCreator`
       - If Not:
         ```
         gsutil iam ch serviceAccount:<PROJECT_ID>@appspot.gserviceaccount.com:objectAdmin gs://<PROJECT_ID>.appspot.com
         ```

     - And then retry again: `gcloud app deploy`

 - ### Test
   - Eitehr use: `gcloud app browse`
   - Or: `https://<PROJECT_ID>.uw.r.appspot.com/`

 - ### Turn off service (If there is only one version, it cannot be stopped)
   - `gcloud app versions list`
   - `gcloud app versions stop VERSION_ID`
   - (Optional) After stop version, we can delete it:
     - `gcloud app versions delete VERSION_ID`


## Step-by-step: Deploy Flask to Google Cloud using Cloud Run:

 - ### Deploy:
 ```
   gcloud services enable cloudbuild.googleapis.com
   gcloud services enable appengine.googleapis.com
   gcloud services enable storage-component.googleapis.com
   
   gcloud run deploy <project_name_or_name_of_the_service> --region=us-west1 --source .
```

 - ### Test:
   - `gcloud projects list` ---> list project with project ID, Number

   - ```
     https://[PROJECT_ID]-[PROJECT_NUMBER].[REGION_ID_NAME].run.app
     https://csci571-assignment02-506083499014.us-west1.run.app
     ```


 - ### Stop:
   ```
   gcloud run services list --region=us-west1
   gcloud run services delete <SERVICE_NAME> --region=us-west1
   ```

