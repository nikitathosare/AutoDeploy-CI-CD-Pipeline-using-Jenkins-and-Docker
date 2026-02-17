pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/your-username/AutoDeploy.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t autodploy-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop autodploy || true
                docker rm autodploy || true
                docker run -d -p 5000:5000 --name autodploy autodploy-app
                '''
            }
        }
    }
}
