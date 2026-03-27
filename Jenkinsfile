pipeline {
    agent any

    stages {
        stage('Clone Code') {
            steps {
                echo 'Cloning Repository...'
                // Note: Checkout is handled automatically by Declarative Pipeline
            }
        }

        stage('SAST Scan - Semgrep') {
            steps {
                // Runs Semgrep in a container; mounts current workspace to /src
                sh 'docker run --rm -v $(pwd):/src returntocorp/semgrep semgrep scan --config auto'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Builds the image locally on the Jenkins host
                    sh 'docker build -t devsecops-app .'
                }
            }
        }

        stage('Container Scan - Trivy') {
            steps {
                // Runs Trivy in a container to scan the image we just built
                // We mount the docker socket so the container can see the host's images
                sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image devsecops-app'
            }
        }

        stage('Run Container') {
            steps {
                // Stops any existing container before running a new one to avoid port conflicts
                sh 'docker stop devsecops-container || true && docker rm devsecops-container || true'
                sh 'docker run -d --name devsecops-container -p 5000:5000 devsecops-app'
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Security scans passed and application deployed!'
        }
        failure {
            echo 'Pipeline failed. Please check the console output for security vulnerabilities or errors.'
        }
    }
}
