pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/Nicholas42-hub/supersetBI.git'
        BRANCH = 'main'
        NEXUS_REPO = 'http://nexus:8081/repository/superset/'
        NEXUS_CREDENTIALS_ID = 'nexus3'
        ARTIFACT_VERSION = '1.0.0-SNAPSHOT'  // This remains the same
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
                script {
                    def artifactPath = "com/example/supersetBI/${ARTIFACT_VERSION}/supersetBI-${ARTIFACT_VERSION}.zip"
                    def localArtifact = "target/supersetBI-1.0.0-zip-assembly.zip" // Corrected filename
                    withCredentials([usernamePassword(credentialsId: "${NEXUS_CREDENTIALS_ID}", passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_USER')]) {
                        sh "curl -v -u ${NEXUS_USER}:${NEXUS_PASS} --upload-file ${localArtifact} ${NEXUS_REPO}${artifactPath}"
                    }
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