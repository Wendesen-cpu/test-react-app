pipeline {
    agent any
    tools {
  nodejs 'nodejs'
}

     environment {
        EC2_USER = 'ubuntu'
        EC2_HOST = '44.201.127.115'
        SSH_KEY = '~/.ssh/demo-key.pem' // Path to your SSH key
        APP_DIR = '/home/ubuntu/next-app' // Deployment directory
    }

    stages {

        stage('Install Dependencies') {
            steps {
                // Install the dependencies
                script {
                    sh 'npm install'
                }
            }
        }

        stage('Build') {
            steps {
                // Build the Next.js app
                script {
                    sh 'npm run build'
                }
            }
        }

        stage('Test') {
            steps {
                // Run tests (optional, if you have tests configured)
                script {
                    sh 'npm run test' // You can adjust this based on your Next.js test setup
                }
            }
        }

       stage('Deploy to EC2') {
            steps {
                script {
                    sh '''
                    echo "Deploying to EC2..."
                    scp -i $SSH_KEY -r ./* $EC2_USER@$EC2_HOST:$APP_DIR
                    ssh -i $SSH_KEY $EC2_USER@$EC2_HOST << EOF
                        cd $APP_DIR
                        npm install --omit=dev
                        pm2 stop next-app || true
                        pm2 start npm --name "next-app" -- run start
                        pm2 save
                    EOF
                    '''
                }
            }
        }
    }

    post {
        always {
            // Clean up steps or notifications (e.g., send an email, Slack message)
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and deploy succeeded.'
        }
        failure {
            echo 'Build or deploy failed.'
        }
    }
}
