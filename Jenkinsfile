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
                // This spins up a temporary Semgrep container, runs the scan, and deletes itself
                sh 'docker run --rm -v "${WORKSPACE}:/src" semgrep/semgrep semgrep scan --config auto'
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
                // VERSION TAG ADDED HERE 👉 aquasec/trivy:0.50.1
                sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:0.50.1 image devsecops-app'
            }
        }

        stage('Run Container') {
            steps {
                // Ignore errors if the container doesn't exist yet (|| true)
                sh 'docker rm -f my-devsecops-app || true'

                // Run the new container with a specific name
                sh 'docker run -d --name my-devsecops-app -p 5000:5000 devsecops-app'
            }
        }
        
    }
}
