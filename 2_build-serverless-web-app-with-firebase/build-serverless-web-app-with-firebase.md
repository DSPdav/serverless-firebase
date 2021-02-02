## Scenario Problems - Solutions
   - The Pet Theory appointment system is **not able to handle the increased load** or allow clients to schedule their own appointments - so build a cloud-based system that easily scale.
In the previous lab, Importing Data to a Firestore Database, you learned how to leverage Firestore to host customer data.
In this lab you will **build a fully fledged Firebase web app that *allows users to log information and schedule appointments* in real time**.

## What I learn?
In this lab, I learn how to:
- Enable the Firebase API in your GCP project.
- Create and configure a Firebase project.
- Configure Firestore Security to automate server-side authentication and authorization.
- Add Google sign-in to your web app.
- Configure your database so users can add their contact information.
- Explore and deploy code that allows users to schedule appointments.
- Explore Firebase's real time updates in your web app.

## Prerequisites
This is a fundamental level lab. This assumes familiarity with the GCP Console and shell environments. Experience with Firebase will be helpful, but is not required.

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

## A. Provisioning the Firebase environment
The primary benefit of Firebase hosting :
- **Serverless**, so there is no infrastructure to manage. 
- Security rules are also embedded within the application, so permissions can be restricted to minimize issues when handling customer data.
- It also has a "pay as you use" model.
Tasks to do :
* Enable the Google Cloud Firestore API.
* Add the Firestore CLI to the GCP project.
* Create a Firebase project.
* Establish security policies.

## B. Enable the Google Cloud Firestore API and Setup a Firestore Database
1. Open the **Navigation menu** and select **APIs & Services** → **Library**. Search and then click **Google Cloud Firestore API**
2. If not already enabled, click **Enable**.
3. Open the **Navigation menu** and select **Firestore**.
4. You will be prompted to choose where you want to store your data. **Select Native Mode**.
5. Select **nam5 (United States)** for your location and then click **Create Database**.
You are now ready to create a Firestore project and link it to your GCP account.

## C. Create a Firebase project
1. Open a new tab in your browser and open the **Firebase console**.
2. Click the account icon in the upper right-hand corner of the page and *ensure that it is the **Qwiklabs student account*** that you have been provisioned for this lab.
3. Then click **Add project**. When prompted for a project name, select your Qwiklabs GCP Project ID from the drop-down menu.
4. Accept the Firebase terms and click **Continue**. Confirm the "Pay as you go" billing plan.
5. Click **Continue** and on the following page *de-select the Google Analytics* for your Firebase Project.
6. Then click **Add Firebase**. Click **Continue** when you see a prompt that says your new project is ready

## D. Register your app
1. After completing the last step, you should be in the Firebase Console. Select the **web icon** ( </> ).
2. When prompted for an "App nickname", type in `<APP_NAME>`.
3. Then check the box next to **Set up Firebase hosting**.
4. Click on the **Register app** button.
5. Click **Next** > **Next** > **Continue to console**.
You have now configured Firebase for the project.

## E. Install the Firebase CLI and deploy to Firebase Hosting
1. In `Cloud Shell`, run the following command to clone the Pet Theory repository:
```
    git clone https://github.com/rosera/pet-theory.git
```
2. Then change your current working directory:
```
    cd pet-theory/lab02
```
3. In `Cloud Shell` run the following npm command :
> to create a new Node app and install the Firebase CLI, a command-line tool
```
    npm init --yes
    npm install -g firebase-tools
```
output:
+ firebase-tools@7.3.0
added 3 packages from 11 contributors, removed 1 package and updated 8 packages in 79.808s

4. Run the following command :
> to link your Google account with Firebase
```
    firebase login --no-localhost
```
5. Enter in **Y** if asked if Firebase can collect error reporting information.
6. Copy and paste the URL generated in a new browser tab and press Enter (directly clicking on the link results in an error.)
7. Select your Qwiklabs Google account and then click Allow. You will then be given an `access code`
8. Copy the `access code`, paste it in the Cloud Shell prompt, and press **Enter**.
output:
✔  Success! Logged in as <STUDENT_EMAIL>

9. Run the following command :
> to set your project region to us-central1 so that it matches up with your Firebase project's resource location
```
    gcloud config set compute/region us-central1
```
10. Now initialize a new Firebase project in your current working directory:
```
    firebase init
```
Running this command will step you through ***setting up your project directory and Firebase products***.
- Which Firebase CLI features do you want to set up for this folder? Press Space to select features, then Enter to confirm your choices.
- Then run through the rest of the steps to configure Firebase:
    * Key down to **Use an existing project** and press **Enter**.
    * Select your <Qwiklabs_GCP_Project_ID> from the list (it's the one that starts with "qwiklabs-gcp-") then **Enter**.
    * Press **Enter** and then **N** to keep your `firestore.rules` file.
    * Press **Enter** and then **Y** to keep your `firestore.indexes.json` file.
    * Press **Enter** to keep your public directory and then **N** to disallow rewrites to your `/index.html` file.
    * Press **Enter** to Set up automatic builds and deploys with GitHub? and press **N**.
    * **Enter** in **N** when prompted to overwrite your `404.html` file.
    * **Enter** in **N** when prompted to overwrite your `index.html` file.
output:

✔  Wrote public/404.html
✔  Wrote public/index.html

i  Writing configuration info to firebase.json...
i  Writing project information to .firebaserc...
i  Writing gitignore file to .gitignore...

✔  Firebase initialization complete!


## F. Set up authentication and a database
Return to the **Firebase Console**
1. Click on the Project Overview button in the left-hand navigation menu.
2. Under the Develop header, select **Authentication** and then click on **Get Started**
3. From the list of Sign-in Providers, click on the **pencil icon** next to the `Google` item.
4. For the "Project support email" select your <Qwiklabs_Google_Account> from the drop down list and click the enable toggle in the top right corner.
5. click on the **Save** button.

## G. Scenario: set up the application
Add the following features to App:
- Set up web authentication for logging in.
- Enable customer details to be logged on the Profile page.
- Create a self service portal for appointments.
Q. Does that mean I'll have to make structural changes every time I want to add something new?
A. You can do most of the heavy lifting with Firebase libraries. Seeing your updates in your browser is as simple as calling `firebase deploy` from the command line.

## H. Configure Firestore authentication and add sign-in to your web app
Return to the **GCP Console**
1. Run the following command :
> to list the folders and files in your lab02 directory:
```
    ls
```
2. Launch the **Open Editor** from Cloud Shell.
3. From the left-hand menu, open the `lab02/firestore.rules` file in the editor. Review the contents of the file.
4. Now open the `public` folder and select the `index.html` to view it in the code editor.

## I. Deploy your application
1. Run the following command :
> to ensure that you are in the `pet-theory/lab02/` directory:
```
    cd pet-theory/lab02/
```
2. Run the following command: 
> to deploy your firebase application:
```
    firebase deploy
```
output:

✔  Deploy complete!

Project Console: https://console.firebase.google.com/project/<Qwiklabs_GCP_Project_ID>/overview
Hosting URL: https://<Qwiklabs_GCP_Project_ID>.web.app

3. Copy the hosting URL and open it in a new tab. Now click on the **Sign in with Google** button.
You have now deployed code to let users use Google authentication to access the appointments app.

The following section is only make some changes in app - feel free to change your source code. REMEMBER! every time you make changes, run `firebase deploy` to deploy your app.
## J. Add a customer page to your web app
## K. Let users schedule appointments
## L. See Firestore Real-Time updates

### Congratulations!
Throughout the course of this lab, you created a robust serverless web application with Firebase. After creating and configuring a Firebase Project, you added Firestore security to automate server-side authentication and authorization. You then went ahead and added Google Sign-in to your web app and configured your database so users can add contact info and appointments. Finally, you explored and deployed code that allows users to schedule appointments and saw Firebase's real time updates in your web app. You are ready to take more labs in this learning path.