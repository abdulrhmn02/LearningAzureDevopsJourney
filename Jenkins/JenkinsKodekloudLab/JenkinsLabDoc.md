# Jenkins CI/CD Pipeline Lab

## Overview

This repository contains the steps and tasks completed during the Jenkins lab at KodeKloud. The lab involved setting up a Jenkins pipeline to perform a series of tasks related to CI/CD.

## Tasks Completed

1. **Install the Pipelines Plugin**
2. **Create a "Hello World" Pipeline**
3. **Clone a Git Repository and Run Tests**
4. **Build a Docker Image**
5. **Deploy the Docker Image**

## Step-by-Step Guide

### 1. Install the Pipelines Plugin

- Open Jenkins in your web browser.
- Go to **Manage Jenkins** > **Manage Plugins**.
- In the **Available** tab, search for "Pipeline" and install the "Pipeline" plugin.

### 2. Create a "Hello World" Pipeline

- Click on **New Item** in the Jenkins dashboard.
- Enter a name for your pipeline (e.g., `hello-world`).
- Select **Pipeline** and click **OK**.
- In the pipeline configuration, add the following script:

```groovy
pipeline {
    agent any
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World!'
            }
        }
    }
}
```

- Save and build the job to see the output.

### 3. Clone a Git Repository and Run Tests

- Create a new pipeline job (e.g., `go-test`).
- Add the following script:

```groovy
pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/kodekloudhub/go-webapp-sample.git'
            }
        }
        stage('Test') {
            steps {
                sh 'go test ./...'
            }
        }
    }
}
```

- Save and build the job to execute the tests.

### 4. Build a Docker Image

- In the same pipeline (or a new one), add a stage to build a Docker image:

```groovy
stage('Build') {
    steps {
        script {
            image = docker.build("adminturneddevops/go-webapp-sample")
        }
    }
}
```

- Ensure that you have the Docker plugin installed and configured.

### 5. Deploy the Docker Image

- Add a final stage to deploy the Docker image:

```groovy
stage('Deploy') {
    steps {
        sh "docker run -p 8090:8000 -d adminturneddevops/go-webapp-sample"
    }
}
```

- Save and build the job to deploy the application.

## Conclusion

This lab has provided valuable insights into Jenkins and CI/CD processes. Feel free to explore and modify the pipeline to suit your needs!
```

---

You can copy and paste the LinkedIn post and `README.md` content as needed. Let me know if you need any adjustments or additional information!