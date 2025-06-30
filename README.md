```markdown
# Node.js Application with Jenkins CI/CD

This repository contains a simple Node.js application with a `Jenkinsfile` to demonstrate a basic CI/CD pipeline for a hands-on Jenkins project.

## 🚀 Overview

The project consists of a "Hello World" style Node.js application that runs on a web server. The primary goal is to showcase how to use a `Jenkinsfile` to automate the process of checking out code, installing dependencies, and running the application.

## 📋 Prerequisites

Before you begin, ensure you have the following installed on your Jenkins server/agent:

* **Jenkins:** An up-and-running Jenkins instance.
* **Node.js and npm:** Required to run the application and install dependencies.
* **Git:** Required for Jenkins to check out the code from your repository.
* **Pipeline Plugin:** Installed in Jenkins to use `Jenkinsfile`.
* **Workspace Cleanup Plugin:** Used for the `cleanWs` step in the pipeline.

## ⚙️ Getting Started

1.  **Fork this repository:**
    Fork this repository to your own GitHub account.

2.  **Create a new Pipeline job in Jenkins:**
    * Go to your Jenkins dashboard and click on "New Item".
    * Enter a name for your job (e.g., "NodeJS-CI-Pipeline").
    * Select "Pipeline" and click "OK".

3.  **Configure the Pipeline:**
    * Scroll down to the "Pipeline" section.
    * For **Definition**, select "Pipeline script from SCM".
    * For **SCM**, select "Git".
    * In **Repository URL**, paste the URL of your forked repository.
    * The **Script Path** should be `Jenkinsfile` by default, which is correct.
    * Click "Save".

4.  **Run the Pipeline:**
    * Click on "Build Now" to start the pipeline.

## 🏗️ Jenkins Pipeline Explained (`Jenkinsfile`)

The `Jenkinsfile` in this repository defines the CI/CD pipeline. It's written using Jenkins' declarative pipeline syntax.

```
```
pipeline {
agent any

stages {
    // Stage 1: Checkout Code
    stage('Checkout Code') {
        steps {
            script {
                checkout scm
            }
        }
    }

    // Stage 2: Install Dependencies
    stage('Dependencies Install') {
        steps {
            script {
                sh 'npm install'
            }
        }
    }

    // Stage 3: Run the Application
    stage('Run App') {
        steps {
            script {
                // Stop any previous instance of the app to avoid port conflicts
                sh 'pkill -f "node app.js" || true'

                // Start the app in the background and redirect logs to app.log
                sh 'nohup node app.js > app.log 2>&1 &'

                // Wait for the application to start
                sleep 60

                // A simple check to see if the application is responding
                sh 'curl http://localhost:3001 || echo "App is not responding!"'
            }
        }
    }
}

post {
    // This block runs after all stages are completed
    always {
        // Cleans up the workspace after the build
        cleanWs(cleanWhenNotBuilt: false,
                deleteDirs: true,
                disableDeferredWipeout: true,
                notFailBuild: true,
                patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                           [pattern: '.propsfile', type: 'EXCLUDE']])
    }
}


}
```

```

### Stages

* **`Checkout Code`**: This stage checks out the source code from the version control system (Git) that you configured in the Jenkins job.
* **`Dependencies Install`**: This stage runs the `npm install` command to download and install all the project dependencies defined in `package.json`.
* **`Run App`**: This stage starts the Node.js application.
    * It first attempts to stop any running instance of the application to prevent errors.
    * It then starts the application in the background using `nohup`, so it continues to run even after the Jenkins job is complete.
    * It waits for 60 seconds to give the app time to start up.
    * Finally, it uses `curl` to check if the application is running and accessible at `http://localhost:3001`.

### Post-build Actions

* **`post`**: The `always` block within `post` ensures that the `cleanWs` step is executed regardless of the build's outcome (success or failure). This helps in maintaining a clean workspace for subsequent builds.

## 📁 File Structure

Here is a brief description of the key files in this repository:

* `app.js`: The main Node.js application file.
* `package.json`: Defines the project's metadata and dependencies.
* `Jenkinsfile`: Contains the definition of the Jenkins pipeline.
* `README.md`: This file, providing information about the project.

```
