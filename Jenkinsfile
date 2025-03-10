pipeline {
    agent any

    environment {
        ENV_FILE_PATH = "/var/lib/jenkins/envs/.env"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']], 
                    extensions: [], 
                    userRemoteConfigs: [[
                        credentialsId: 'wasim-zaman-jenkins-credentials', 
                        url: 'https://github.com/Wasim-Zaman/jenkins-practice.git'
                    ]]
                )
            }
        }

        stage('Setup Environment File') {
            steps {
                echo "Copying environment file to the backend..."
                sh "test -f ${ENV_FILE_PATH} && cp ${ENV_FILE_PATH} ${WORKSPACE}/.env || echo 'Environment file not found at ${ENV_FILE_PATH}'"
            }
        }

        stage('Manage PM2 and Install Dependencies') {
            steps {
                script {
                    echo "Stopping PM2 process if running..."
                    def processStatus = sh(script: 'pm2 list', returnStdout: true).trim()
                    if (processStatus.contains('jenkins-practice')) {
                        sh 'pm2 stop jenkins-practice || true'
                        sh 'pm2 delete jenkins-practice || true'
                    }
                }
                echo "Installing dependencies..."
                sh 'npm install'
                echo "Generating Prisma files..."
                sh 'npx prisma generate || echo "Skipping Prisma generation - not required or not installed"'
                echo "Starting application with PM2..."
                sh 'pm2 start "npm start" --name jenkins-practice'
                echo "PM2 process started."
                sh 'pm2 save'
            }
        }
    }
}
