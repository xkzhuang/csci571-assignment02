# CSCI571 Assignment 02

1. Deployment:
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
   