pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                echo 'Cloning Repository...'
            }
        }

        stage('SAST Scan - Semgrep') {
            steps {
                sh 'semgrep scan --config auto'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("devsecops-app")
                }
            }
        }

        stage('Container Scan - Trivy') {
            steps {
                sh 'trivy image devsecops-app'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 5000:5000 devsecops-app'
            }
        }
    }
}
