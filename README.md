# Node.js Application with Jenkins CI/CD

This repository contains a simple Node.js application along with a `Jenkinsfile` to demonstrate a basic CI/CD pipeline for a hands-on Jenkins project.

---

## 🚀 Overview

The project consists of a basic "Hello World" style Node.js application that runs on a web server.  
The main goal is to showcase how to use a `Jenkinsfile` to automate:

- Checking out code
- Installing dependencies
- Running the application

---

## 📋 Prerequisites

Before you begin, ensure the following are installed on your Jenkins server/agent:

- **Jenkins**: A running Jenkins instance
- **Node.js and npm**: Required to run the application and install dependencies
- **Git**: Needed for Jenkins to clone the repository
- **Pipeline Plugin**: Installed in Jenkins to use the `Jenkinsfile`
- **Workspace Cleanup Plugin**: Used for the `cleanWs` step in the pipeline

---

## ⚙️ Getting Started

### 1. Fork this Repository

- Fork this repository to your own GitHub account.

### 2. Create a New Pipeline Job in Jenkins

1. Go to your Jenkins dashboard and click **"New Item"**
2. Enter a name for your job (e.g., `NodeJS-CI-Pipeline`)
3. Select **"Pipeline"** and click **OK**

### 3. Configure the Pipeline

- Scroll down to the **Pipeline** section
- For **Definition**, select **Pipeline script from SCM**
- For **SCM**, select **Git**
- In **Repository URL**, paste the URL of your forked repository
- The **Script Path** should be `Jenkinsfile` (default)
- Click **Save**

### 4. Run the Pipeline

- Click on **"Build Now"** to start the pipeline

---

## 🏗️ Jenkins Pipeline Explained (`Jenkinsfile`)

The `Jenkinsfile` defines the CI/CD pipeline using Jenkins' **declarative syntax**:

```groovy
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
            cleanWs(
                cleanWhenNotBuilt: false,
                deleteDirs: true,
                disableDeferredWipeout: true,
                notFailBuild: true,
                patterns: [
                    [pattern: '.gitignore', type: 'INCLUDE'],
                    [pattern: '.propsfile', type: 'EXCLUDE']
                ]
            )
        }
    }
}
```
## 🔍 Stage Descriptions

### ✅ Checkout Code
Pulls the source code from your Git repository.

### 📦 Dependencies Install
Runs `npm install` to install all required Node.js packages defined in `package.json`.

### 🚀 Run App
- Stops any existing instance of the app to avoid port conflicts  
- Starts the app in the background using `nohup`  
- Waits 60 seconds to allow the app to initialize  
- Uses `curl` to check if the app is running and responding at `http://localhost:3001`

---

## 🔁 Post-build Actions

The `post` block ensures that the `cleanWs` step runs regardless of build success or failure.  
This helps maintain a clean Jenkins workspace for future builds.

---

## 📁 File Structure

| File           | Description                                        |
|----------------|----------------------------------------------------|
| `app.js`       | The main Node.js application file                  |
| `package.json` | Contains project metadata and dependencies         |
| `Jenkinsfile`  | Defines the Jenkins CI/CD pipeline                 |
| `README.md`    | Project overview and setup instructions (this file) |
