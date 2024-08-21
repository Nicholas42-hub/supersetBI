pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/Nicholas42-hub/supersetBI.git'
        BRANCH = 'main' // Replace with the branch you want to use
        NEXUS_REPO = 'http://localhost:8081/repository/superset/' // Updated Nexus repository URL
        NEXUS_CREDENTIALS_ID = 'nexus3' // Use the credential ID 'nexus3'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GITHUB_REPO}"
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Zip Artifacts') {
            steps {
                sh 'zip -r artifact.zip *' // Zips all files in the current directory
            }
        }

        stage('Push to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                    sh "curl -u ${NEXUS_USER}:${NEXUS_PASS} --upload-file artifact.zip ${NEXUS_REPO}artifact.zip"
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean up the workspace after the pipeline completes
        }
    }
}