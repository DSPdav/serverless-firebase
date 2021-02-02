## Scenario Problems - Solutions
In this lab you will **create a pipeline for deploying websites** based on Hugo, a static website builder. 
- You will store the website content in Cloud Source Repositories and deploy the website with Firebase, then use Cloud Build to create a pipeline to automatically deploy new content that is committed to the repository.

## What I learn?
In this lab, I learn the following:
- An overview of static websites
- Setting up a website with Hugo
- Storing the website content in Cloud Source Repositories
- Deploying the website with Firebase
- Creating a build pipeline with Cloud Build to automate the deployment

## Prerequisites
The instructions provided here are sufficient to guide you through this lab.

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

## A. Process overview
The goal is to be able to commit code and have it trigger the pipeline which will in turn deploy the website.
Your journey will be divided into two parts. 
- First, you will build the website locally and deploy it to Firebase manually so you can gain an understanding of the entire process.
- Second, you will automate the process by building a pipeline with Cloud Build.

## B. Manual deployment
- Connect to the Linux instance
1. From the Navigation Menu select **Compute Engine** > **VM Instances**. You will see one instance that has been built for you.
2. At the end of the line you should see an <External_IP> address and an SSH button.
3. Click **SSH**. A window will appear and you will see a **Linux instance shell** prompt.

- Install Hugo locally
1. In the **Linux instance shell**, examine the file installhugo.sh by running `cat /tmp/installhugo.sh` and see the contents.
Note: `wget` command to download Hugo and the `tar` command to unpack the Hugo archive.
2. Enter the commands below:
> to run the script and install Hugo
```
    cd ~
    /tmp/installhugo.sh
```

- Create a repository and the initial web site
Now **create a Cloud Source Repository** to hold the web site and then clone the repository to the Linux instance. Cloning a repository creates a mirror of it in the shell. This allows you to implement the web site while in the shell and later commit your changes to the file system.
1. Enter the following commands in the **Linux instance shell**:
```
    cd ~
    gcloud source repos create my_hugo_site
    gcloud source repos clone my_hugo_site
```
Note: ignore the two warning messages about the charge for the repository and that the repository is empty.
2. Now you are ready to create the site structure. Enter the commands below in the Linux shell.
```
    cd ~
    /tmp/hugo new site my_hugo_site --force
```
Note: The `--force` option will create the site in the repository directory, which already exists. This allows you to keep the Git-related information in the directory that you just cloned.
3. Now install the Ananke theme to provide a layout for your site. Enter the following commands in the Linux instance shell:
```
    cd ~/my_hugo_site
    git submodule add \
    https://github.com/budparr/gohugo-theme-ananke.git \
    themes/ananke
    echo 'theme = "ananke"' >> config.toml
```
4. With the structure of the web site set up, you can now preview it. Enter the command below to launch the site at TCP port 8080:
```
    cd ~/my_hugo_site
    /tmp/hugo server -D --bind 0.0.0.0 --port 8080
```
Hugo will build the site and serve it for access on TCP port 8080. The server will run until it is stopped by pressing **Ctrl+C**.
5. Open a browser tab and browse to `http://<EXTERNAL_IP>:8080`
6. Go back to the Linux shell and press **Ctrl+C** to stop the Hugo server.

- Add Firebase to your project
1. Go to the Firebase console.
2. Click **Add project**. You will be asked to select a name for your project. Click inside of the name field and select your existing Google Cloud project that starts with "qwiklabs-gcp-...".
3. Accept the Firebase terms, then click **Continue**.
4. You may be asked to confirm the Firebase billing plan. The Firebase costs are included with the lab. If you are prompted, click **Confirm plan**.
5. You will be asked to acknowledge some of the criteria when adding Firebase to a project. Click **Continue**.
6. You will be asked to confirm the use of Google Analytics for this Firebase project. Since this is a lab environment, use the toggle to disable Google Analytics and click **Add Firebase**.
7. Click **Continue** if prompted after Firebase is added.

- Deploy the site to Firebase
1. Install Fireball CLI in the Linux instance shell:
```
    curl -sL https://firebase.tools | bash
```
2. ow you need to initialize Firebase. Enter the command below into the shell:
```
    cd ~/my_hugo_site
    firebase init
```
3. Select **Hosting** using the arrow keys and spacebar. When asked for a project option, select **Use an existing project**, then use the arrow keys, spacebar, and the **Enter** key to select the Project ID provided on the lab instruction page. For the public directory, select the default value public. For configuring as a single page application, select the default value of **N**. For setting up automatic builds and deploys with GitHub, select **N**.
If asked to overwrite any existing files, select **Y**.
4. You are ready to deploy the application. Enter the commands below into the Linux instance shell:
> to rebuild the site with Hugo and to deploy it with Firebase
```
    /tmp/hugo && firebase deploy
```
5. After the application has been deployed, you will receive a `Hosting URL`. Click on it and you will see the same website being served from the Firebase CDN (content delivery network).


## C. Automate the deployment
- Perform the initial commit
The goal of building the pipeline is ***to be able to trigger builds when changes are made to the repository***.
1. Configure the git commands global parameters by entering the commands below into the Linux shell:
```
    git config --global user.name "<GIT_NAME>"
    git config --global user.email "<GIT_EMAIL>"
```
2. Enter the commands below in the Linux shell :
> to create a `.gitignore` file to exclude certain directories from the repository
```
    cd ~/my_hugo_site
    echo "resources" >> .gitignore
```
3. Perform the initial commit to the repository by entering the commands below:
```
    git add .
    git commit -m "Add app to Cloud Source Repositories"
    git push -u origin master
```

- Configure the build
Cloud Build uses a file named `cloudbuild.yaml` in the root directory of the repository to perform the build. The file is in YAML format.
1. Enter the command below in the Linux shell. Note the final period (".") at the end of the cp command:
```
    cd ~/my_hugo_site
    cp /tmp/cloudbuild.yaml .
```
2. Run the following to see what the `cloudbuild.yaml` file looks like.
```
    cat cloudbuild.yaml
```
Here are some observations about the `cloudbuild.yaml` file:
- There are **three named steps** in this file each of which is performed by a container image. The first two steps use a Google-supported builder to use wget to download the Hugo and Firebase tools. These two steps run in parallel. Using the wget builder is faster than installing wget manually.
- The third step uses a standard Ubuntu container to install Hugo and Firebase after which the site is built and deployed. Installing Hugo and Firebase for each deployment allows you to change the version of Hugo whenever you desire while also using the latest version of Firebase.
- The `tar` and `wget` commands are nearly identical to those used earlier in the installhugo.sh script.
- The file also uses a custom substitution variable (_HUGO_VERSION) and a Google-provided substitution variable (PROJECT_ID) to allow for this template to be used in different environments.
- The Hugo and Firebase binaries are created and installed in a temporary directory so that they do not inadvertently get deployed to the website itself.

- Create the Cloud Build trigger
Now create a trigger that will respond to commits to the master branch of the repository.
1. In the Cloud Console, navigate to Navigation Menu > **Cloud Build** > **Triggers**.
2. Click  **ADD TRIGGER**.
3. For the trigger configuration, enter the following details:
| Filed                                   | Value                                         |
| --------------------------------------- | --------------------------------------------- |
| Name                                    | commit-to-master-branch                       |
| Description                             | Push to master                                |
| Event                                   | Push to a branch                              |
| Repository	                          | my_hugo_site                                  |
| Branch (regex)	                      | ^master$ (be sure Invert Regex is unchecked)  |
| Build Configuration	                  | Cloud Build configuration file (yaml or json) |
| Cloud Build Configuration file location | / cloudbuild.yaml                             |

4. Click **Create**.

- Update the Cloud Build service account
The Cloud Build Service account needs to have permissions to use Firebase to deploy the website.
1. From the Cloud Console Navigation menu, select **IAM & Admin** > **IAM**.
2. Make sure you pick the service account `cloudbuild.gserviceaccount.com`. Click the **edit icon**, then click **ADD ANOTHER ROLE** and add the role **Firebase Products** > **Firebase Hosting Admin** to it.

- Test the pipeline
Now that you have created the pipeline, you can make a change to the site then commit it to see if the change propagates.
1. In the Linux shell enter the command below to move to the repository directory:
```
    cd ~/my_hugo_site
```
2. Edit the file `config.toml` and change the title
```
    vim config.toml
```
3. In the Linux shell, enter the commands below to commit the changes to the repository and trigger the Cloud Build pipeline:
```
    git add .
    git commit -m "I updated the site title"
    git push -u origin master
```
3. Go to the **Cloud Build console** and check the build history. You should see a successful deployment. Browse to the **Hosting URL**

### Congratulations!
You have learned how Cloud Build can orchestrate a pipeline to quickly deploy Hugo websites to Firebase, which provides a CDN and SSL certificate. Cloud Build allows you to tailor the process to adapt to your needs. The short deployment times allow you to innovate quickly and test your website revisions with little effort. Consult the Cloud Build and Firebase documentation for more information.