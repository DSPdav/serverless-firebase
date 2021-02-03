## Scenario Problems - Solutions
   1. The Pet Theory chain of veterinary clinics appointment scheduling system is ***not able to handle the increased load*** - so Lily is asking you to build a cloud-based system that scales better than the legacy solution.
   2. Pet Theory's ***Ops team is a single person*** - so they need a solution that doesn't require lots of ongoing maintenance.

## What I learn?
In this lab, I learn how to:
   - Set up Firestore in GCP.
   - Write database import code.
   - Generate a collection of customer data for testing.
   - Import the test customer data into Firestore.
   - Manipulate data in Firestore through the Console.
   - Add a developer to a GCP project without giving them Firestore access.

## Prerequisites
This is a fundamental level lab. This assumes familiarity with the GCP Console and shell environments. Experience with Firebase will be helpful, but is not required.

## Setup
This lab provisions **two usernames**. One to login to Google Cloud. Another for learning how to add a developer to a Google Cloud project without giving them Firestore access.

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

## A. Set up Firestore in GCP
Create Firestore Database:
1. In the Google Cloud Console, go to the Navigation menu and **select Firestore**
2. Click the **Select Native Mode** button.
3. In the **Select a location** dropdown, choose a database region closest to your location and then click **Create Database**.

## B. Write database import code
The new Cloud Firestore database is in place, but it's empty - migrating the customer data to the new Firestore database:
NOTE: The customer data will be available in a **CSV file** - create an app that reads customer records from a CSV file and writes them to Firestore.
1. In Cloud Shell, run the following command to clone the Pet Theory repository:
```
    git clone https://github.com/rosera/pet-theory
```
2. **Use the Cloud Shell Code Editor** to edit your files. From the top ribbon of your Cloud Shell session, click on the **Open in new window** icon, it will open a new tab. Then click **Open Editor**
3. Then change your current working directory to lab01:
```
    cd pet-theory/lab01
```
> check `package.json`
4. To allow Patrick's code *to write to the Firestore database*, you need to install some additional peer dependancies. Run the following command to do so:
```
    npm install @google-cloud/firestore
```
5. To enable the app to write logs to Stackdriver Logging, install an additional module:
```
    npm install @google-cloud/logging
```
> check `package.json`
6. Look at `importTestData.js` and update it!

## C. Create test data
Time to import some test data! - not real customer data, because customer data may include personal identifiable information, also called PII.
1. install the "faker" library, which will be used by the script that generates the fake customer data.
```
    npm install faker
```
2. open the file named `createTestData.js` and update it!
3. Run the following command to configure your GCP Project ID in Cloud Shell
```
    gcloud config set project <PROJECT_ID>
```
4. Set the project ID as an environment variable:
```
    PROJECT_ID=$(gcloud config get-value project)
```
5. Run the following command in Cloud Shell to create the file `customers_1000.csv`, which will contain 1000 records of test data:
```
    node createTestData 1000
```
output:
Created file customers_1000.csv containing 1000 records.

6. Open the file `customers_1000.csv` and verify that the test data has been created.

## D. Import the test customer data
1. Test the import capability, use both the import script and the test data created earlier
```
    node importTestData customers_1000.csv
```
output:
Writing record 500
Writing record 1000
Wrote 1000 records

NOTE: If you get an error that resembles the following:
`Error: Cannot find module 'csv-parse'`
Run the following command to add the csv-parse package to your environment:
```
    npm install csv-parse
```
2. At this point, if you are feeling adventurous, feel free 
   - to create a larger test data file and 
   - import it into the Firestore database!
```
    node createTestData 20000
    node importTestData customers_20000.csv
```

## E. Inspect the data in Firestore
After successfully migrated the test data to the Firestore database. Open up Firestore and see the results!
1. Return to your GCP Console tab. In the Navigation menu click on **Firestore**.
2. Refresh your browser tab and you should see the list of customers successfully migrated

## F. Edit and delete data from Firestore
feel free to edit/delete a fake customer data

## G. Add a developer to the project without giving them Firestore access
1. Now add these two roles to the developer, replacing `<EMAIL>` with the user 2nd User ID you have for this lab:
a. a role called `roles/logging.viewer` that lets a member read the logs.
```
    gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=user:<EMAIL> --role=roles/logging.viewer
```
b. a role called `roles/source.writer` that lets a member read and write to the source control repository.
```
    gcloud projects add-iam-policy-binding $PROJECT_ID \
    --member=user:[EMAIL] --role roles/source.writer
```
2. Test the account - login with 2nd User ID

### Congratulations!
Throughout the course of this lab, you received hands-on practice with Firestore. After generating a collection of customer data for testing, you ran a script that imported the data into Firestore. You then learned how to manipulate data in Firestore through the GCP Console. Finally, you added a developer to a Google Cloud project without giving them Firestore access.