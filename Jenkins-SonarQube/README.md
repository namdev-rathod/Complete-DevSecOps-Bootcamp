## 🚀 Jenkins + SonarQube End-to-End Setup Guide

This section provides a step-by-step guide to set up Jenkins with SonarQube for CI/CD and code quality analysis.

### 1. Jenkins Installation

1. Download Jenkins from the [official site](https://www.jenkins.io/download/).
2. Run the installer and follow the prompts.
3. After installation, Jenkins runs at `http://localhost:8080`.
4. Unlock Jenkins using the initial admin password from `C:\Program Files\Jenkins\secrets\initialAdminPassword`.
5. Install suggested plugins and create an admin user.

### 2. SonarQube Installation

1. Download SonarQube Community Edition from [here](https://www.sonarqube.org/downloads/).
2. Extract the zip file.
3. Install Java 11+ (required for SonarQube).
4. Start SonarQube:
   - Open a terminal, navigate to `sonarqube\bin\windows-x86-xx\`
   - Run `StartSonar.bat`
5. Access SonarQube at `http://localhost:9000`.
6. Default credentials: `admin` / `admin` (change password on first login).

### 3. Generate SonarQube Token

1. Log in to SonarQube (`http://localhost:9000`).
2. Go to your profile (top right) → My Account → Security.
3. Under "Generate Tokens", enter a name (e.g., `jenkins-token`) and click "Generate".
4. Copy the token and save it securely (you’ll need it for Jenkins).

### 4. Jenkins: Install Required Plugins

- Go to Jenkins Dashboard → Manage Jenkins → Manage Plugins.
- Install:
  - SonarQube Scanner
  - Pipeline
  - Blue Ocean (optional, for better UI)

### 5. Configure SonarQube in Jenkins

1. Jenkins Dashboard → Manage Jenkins → Configure System.
2. Scroll to "SonarQube servers" section.
3. Click "Add SonarQube".
   - Name: `SonarQube`
   - Server URL: `http://localhost:9000`
   - Server authentication token: Add → Paste the token generated earlier.
4. Save.

### 6. Configure SonarQube Scanner in Jenkins

1. Jenkins Dashboard → Manage Jenkins → Global Tool Configuration.
2. Find "SonarQube Scanner".
3. Click "Add SonarQube Scanner".
   - Name: `SonarQubeScanner`
   - Install automatically (recommended).
4. Save.

### 7. Example Jenkins Declarative Pipeline with SonarQube

Add a `Jenkinsfile` to your project root:

```groovy
pipeline {
    agent any

    tools {
        // Use SonarQube Scanner installed in Jenkins
        sonarQube 'SonarQubeScanner'
    }

    environment {
        // Name must match the SonarQube server config in Jenkins
        SONARQUBE_SERVER = 'SonarQube'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                // Replace with your build tool, e.g., Maven/Gradle/npm
                echo 'Building...'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    // Replace with your project-specific analysis command
                    sh 'sonar-scanner'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                // Wait for SonarQube quality gate result
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
```

**Note:**
- For Windows agents, replace `sh 'sonar-scanner'` with `bat 'sonar-scanner.bat'`.
- Make sure your project has a valid `sonar-project.properties` file.

### 8. `sonar-project.properties` Example

Add this file to your project root:

```
sonar.projectKey=your_project_key
sonar.projectName=Your Project Name
sonar.sources=src
```

### 9. Run the Pipeline

- Commit and push your `Jenkinsfile` and `sonar-project.properties`.
- Create a new pipeline job in Jenkins, point it to your repo.
- Run the job. Jenkins will build, analyze with SonarQube, and enforce the quality gate.

---

## 🎓 Outcome

By the end of this bootcamp, you'll be able to:

✅ Build secure GitHub CI/CD pipelines  
✅ Integrate security at every stage of SDLC  
✅ Scan, alert and fix issues automatically  
✅ Leverage AI for code and pipeline hardening  
✅ Present real-world secure DevSecOps projects to employers
