pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube'
        EMAIL_TO = 'rohit.shinde@sumasoft.net'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rohitshinde0209/Wanderlust-Mega-Project'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo "Building Docker images"
                sh 'docker build -t wanderlust-backend ./backend'
                sh 'docker build -t wanderlust-frontend ./frontend'
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                echo "Running OWASP Dependency Check"
            }
        }

        stage('Trivy Scan') {
            steps {
                echo "Running Trivy Scan"
                sh 'trivy image wanderlust-backend'
                sh 'trivy image wanderlust-frontend'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    echo "Running SonarQube Analysis"
                }
            }
        }

        stage('Deploy Application') {
            steps {
                echo "Deploying application using Docker"
                sh 'docker compose down'
                sh 'docker compose up -d'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "SUCCESS: Jenkins Job ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                Deployment Successful!

                Job: ${env.JOB_NAME}
                Build Number: ${env.BUILD_NUMBER}
                URL: ${env.BUILD_URL}
                """,
                to: "${EMAIL_TO}"
            )
        }

        failure {
            emailext(
                subject: "FAILED: Jenkins Job ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                Deployment Failed!

                Job: ${env.JOB_NAME}
                Build Number: ${env.BUILD_NUMBER}
                URL: ${env.BUILD_URL}
                """,
                to: "${EMAIL_TO}"
            )
        }
    }
}
