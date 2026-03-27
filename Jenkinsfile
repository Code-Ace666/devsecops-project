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
        // Run Trivy inside a container, giving it access to the server's Docker daemon so it can see your newly built image
        sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image devsecops-app'
    }
}

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 5000:5000 devsecops-app'
            }
        }
    }
}
