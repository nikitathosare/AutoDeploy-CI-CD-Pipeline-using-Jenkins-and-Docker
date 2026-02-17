pipeline {
    agent any

    environment {
        // ===== Deployment Server Details =====
        EC2_USER = "ubuntu"
        EC2_IP   = "51.20.252.245"

        // ===== Application Details =====
        APP_DIR    = "/home/ubuntu/mood-journal-app"
        APP_NAME   = "moodjournal"
        IMAGE_NAME = "mood-journal-image"
        PORT       = "5000"

        // ===== Jenkins Credential ID =====
        CREDENTIAL_ID = "jenkins"
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "📥 Cloning Mood Journal source code from GitHub"
                git branch: 'main',
                    url: 'https://github.com/nikitathosare/mood-journa.git'
            }
        }

        stage('Deploy to EC2 Server') {
            steps {
                echo "🚀 Deploying Mood Journal application to EC2 server"

                sshagent([CREDENTIAL_ID]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} "
                        set -e

                        echo '📂 Checking Mood Journal app directory'
                        if [ ! -d ${APP_DIR} ]; then
                            echo '📥 Directory not found, cloning Mood Journal repository'
                            git clone https://github.com/nikitathosare/mood-journa.git ${APP_DIR}
                        fi

                        cd ${APP_DIR}

                        echo '🛑 Stopping old Mood Journal container (if exists)'
                        docker stop ${APP_NAME} || true
                        docker rm ${APP_NAME} || true

                        echo '🐳 Building Mood Journal Docker image'
                        docker build -t ${IMAGE_NAME} .

                        echo '🚀 Running new Mood Journal Docker container'
                        docker run -d -p ${PORT}:${PORT} --name ${APP_NAME} ${IMAGE_NAME}

                        echo '✅ Mood Journal application deployed successfully'
                    "
                    """
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Mood Journal CI/CD Pipeline executed successfully!"
        }
        failure {
            echo "❌ Mood Journal CI/CD Pipeline failed. Please check logs."
        }
    }
}
