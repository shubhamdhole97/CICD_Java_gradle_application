pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code from SCM (Git)
                checkout scm
            }
        }

        stage('Sonar Quality Check') {
            agent {
                docker {
                    image 'openjdk:11'
                    args '-u root:root'  // optional: run as root if permission issues occur
                }
            }
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        // Make gradlew executable
                        sh 'chmod +x gradlew'
                        // Run the SonarQube analysis using Gradle wrapper
                        sh './gradlew sonarqube'
                    }

                    // Wait for SonarQube quality gate status
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }
}
