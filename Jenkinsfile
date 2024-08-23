pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/Nicholas42-hub/supersetBI.git'
        BRANCH = 'main'
        NEXUS_REPO = 'http://nexus:8081/repository/superset/'
        NEXUS_CREDENTIALS_ID = 'nexus3'
        ARTIFACT_VERSION = '1.0.0-SNAPSHOT'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${GITHUB_REPO}"
            }
        }

        stage('Prepare Copies with Different metadata.yaml') {
            steps {
                script {
                    // Copy original folder to create three different versions
                    sh 'cp -r dashboard_export_20240724T154058 dashboard_export_dashboard'
                    sh 'cp -r dashboard_export_20240724T154058 dashboard_export_sql'
                    sh 'cp -r dashboard_export_20240724T154058 dashboard_export_charts'

                    // Modify metadata.yaml for each version
                    sh "sed -i 's/type:.*/type: Dashboard/' dashboard_export_dashboard/metadata.yaml"
                    sh "sed -i 's/type:.*/type: sql/' dashboard_export_sql/metadata.yaml"
                    sh "sed -i 's/type:.*/type: charts/' dashboard_export_charts/metadata.yaml"
                }
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