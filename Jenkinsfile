pipeline {
    agent any

    environment {
        // ===== Deployment Server Details =====
        EC2_USER = "ubuntu"
        EC2_IP = "51.20.252.245"

        // ===== Application Details =====
        APP_DIR = "/home/ubuntu/flask-weather-app"
        APP_NAME = "python"
        IMAGE_NAME = "mypyapp"
        PORT = "5000"

        // ===== Jenkins Credential ID =====
        CREDENTIAL_ID = "jenkins"
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Cloning source code from GitHub"
                git branch: 'main',
                    url: 'https://github.com/nikitathosare/AutoDeploy-CI-CD-Pipeline-using-Jenkins-and-Docker.git'
            }
        }

        stage('Deploy to EC2 Server') {
            steps {
                echo "Deploying application to remote EC2 server"

                sshagent([CREDENTIAL_ID]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} << 'EOF'

                    echo "🔹 Moving to application directory"
                    if [ ! -d "${APP_DIR}" ]; then
                        echo "📂 Directory not found. Cloning repository..."
                        git clone https://github.com/nikitathosare/AutoDeploy-CI-CD-Pipeline-using-Jenkins-and-Docker.git ${APP_DIR}
                    fi

                    cd ${APP_DIR}

                    echo "🛑 Stopping old container (if exists)"
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true

                    echo "🐳 Building Docker image"
                    docker build -t ${IMAGE_NAME} .

                    echo "🚀 Running new container"
                    docker run -d \\
                        -p ${PORT}:${PORT} \\
                        --name ${APP_NAME} \\
                        ${IMAGE_NAME}

                    echo "✅ Deployment completed successfully"
                    EOF
                    """
                }
            }
        }
    }

    post {
        success {
            echo "🎉 CI/CD Pipeline executed successfully!"
        }
        failure {
            echo "❌ CI/CD Pipeline failed. Check logs."
        }
    }
}
