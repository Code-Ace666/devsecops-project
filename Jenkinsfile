pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                echo 'Cloning Repository...'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("devsecops-app")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 5000:5000 devsecops-app'
            }
        }
    }
}
