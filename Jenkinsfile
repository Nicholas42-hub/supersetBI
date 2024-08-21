pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/Nicholas42-hub/supersetBI.git'
        BRANCH = 'main'
        NEXUS_REPO = 'http://nexus:8081/repository/superset/'
        NEXUS_CREDENTIALS_ID = 'nexus3'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GITHUB_REPO}"
            }
        }

        stage('Build and Package with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Push to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -u ${NEXUS_USER}:${NEXUS_PASS} --upload-file target/supersetBI-1.0.0-zip-assembly.zip ${NEXUS_REPO}supersetBI-1.0.0.zip"
                }
            }
        }
    }

}