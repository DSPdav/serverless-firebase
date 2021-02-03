## Prerequisites
An intermediate level of knowledge on Google Cloud serverless technologies is recommended prior to undertaking this lab.
In this challenge lab you will be assessed on your knowledge of the following areas:
- Firestore
- Cloud Run
- Cloud Build
- Container Registry

## How to start your lab and sign in to the Google Cloud Console?
1. Click the Start Lab button.
2. Copy the username, and then click Open Google Console.
3. In the Sign in page, paste the username that you copied from the Connection Details panel.

## How to activate Cloud Shell?
1. In the Cloud Console, in the top right toolbar, click the Activate Cloud Shell button. Click Continue.
2. list the active account name with this command:
```
    gcloud auth list
```
3. You can list the project ID with this command:
```
    gcloud config list project
```

## Provision the environment
Run the following commands first in **Cloud Shell**
Link to the project:
```
    gcloud config set project $(gcloud projects list --format='value(PROJECT_ID)' --filter='qwiklabs-gcp')
```
Clone the repo:
```
    git clone https://github.com/rosera/pet-theory.git
```

## Challenge scenario
In this lab you will create a frontend solution using a Rest API and Firestore database.

## Task 1: Create a Firestore database
To complete this section successfully, you are required to implement the following:
- Cloud Firestore Database
- Use Firestore `Native Mode`
- Add location `Nam5 (United States)`
### Step-by-step
Create Firestore Database:
1. In the Google Cloud Console, go to the Navigation menu and **select Firestore**
2. Click the **Select Native Mode** button.
3. In the **Select a location** dropdown, choose a database region and then click **Create Database**.

## Task 2: Populate the Database
To complete this section successfully, you are required to implement the following tasks:
- Use the sample code from `pet-theory/lab06/firebase-import-csv/solution`
```
    cd pet-theory/lab06/firebase-import-csv/solution
    npm install
```
- To import CSV use the node `pet-theory/lab06/firebase-import-csv/solution/index.js`
```
    node index.js netflix_titles_original.csv
```
> Verify the Firestore Database has been updated by viewing the data in the Firestore UI.


Run the following Tasks in **Cloud Shell**.
## Task 3: Create a REST API
To complete this section successfully, you are required to implement the following tasks:
- Access `pet-theory/lab06/firebase-rest-api/solution-01`
```
    cd ~
    cd pet-theory/lab06/firebase-rest-api/solution-01
```
- Build and Deploy the code to Google Container Registry
```
    gcloud builds submit --tag gcr.io/<PROJECT_ID>/rest-api:0.1
    gcloud run deploy --image gcr.io/<PROJECT_ID>/rest-api:0.1
```
- Deploy the image as a Cloud Run Service
- `curl -X GET $SERVICE_URL` should respond with:
{"status":"Netflix Dataset! Make a query."}

## Task 4: Firestore API access
To complete this section successfully, you are required to implement the following tasks:
- Access `pet-theory/lab06/firebase-rest-api/solution-02`
```
    cd ~
    cd pet-theory/lab06/firebase-rest-api/solution-02
```
- Build the updated application
```
    gcloud builds submit --tag gcr.io/<PROJECT_ID>/rest-api:0.2
    gcloud run deploy --image gcr.io/<PROJECT_ID>/rest-api:0.2
```
- Use Cloud Build to tag and deploy image revision to Container Registry
- Deploy the new image as Cloud Run service
- `curl -X GET $SERVICE_URL/2019` should respond with json dataset

## Task 5: Deploy the Staging Frontend
To complete this section successfully, you are required to implement the following tasks:
- Access `pet-theory/lab06/firebase-frontend`
```
    cd ~
    cd pet-theory/lab06/firebase-frontend
```
- Build the frontend staging application
```
    gcloud builds submit --tag gcr.io/<PROJECT_ID>/frontend-staging:0.1
    gcloud run deploy --image gcr.io/<PROJECT_ID>/frontend-staging:0.1
```
- Use Cloud Build to tag and deploy image revision to Container Registry
- Deploy the new image as Cloud Run service
- Frontend access to Rest API and Firestore Database
> Access the Frontend Service URL.
Note: It's using a demo dataset to provide the onscreen entries

## Task 6: Deploy the Production Frontend
To complete this section successfully, you are required to implement the following tasks:
- Access `pet-theory/lab06/firebase-frontend/public`
```
    cd public
    ls
```
- Update the frontend application i.e. `app.js` to use the REST API
```
    vim app.js
    cd ..
```
- Dont forget to append the year to the SERVICE_URL
- Use Cloud Build to tag and deploy image revision to Container Registry
```
    gcloud builds submit --tag gcr.io/<PROJECT_ID>/frontend-production:0.1
    gcloud run deploy --image gcr.io/<PROJECT_ID>/frontend-production:0.1
```
- Deploy the new image as Cloud Run service
- Frontend access to Rest API and Firestore Database
Now that the services have been deployed you will be able to see the contents of the Firestore database using the frontend service.