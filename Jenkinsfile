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

      
        stage('Deploy Built Files to EC2') {
            steps {
                script {
                    // Copy only the build output to the EC2 instance
                    sh '''
                    echo "Deploying built files to EC2..."
                    scp -i $SSH_KEY -r .next/ $EC2_USER@$EC2_HOST:$APP_DIR/.next
                    scp -i $SSH_KEY -r package.json package-lock.json $EC2_USER@$EC2_HOST:$APP_DIR/
                    '''
                }
            }
        }

        stage('Start App on EC2') {
            steps {
                script {
                    // SSH into EC2 and install production dependencies
                    sh '''
                    echo "Starting app on EC2..."
                    ssh -i $SSH_KEY $EC2_USER@$EC2_HOST << EOF
                        cd $APP_DIR
                        npm install --omit=dev  # Install only production dependencies
                        pm2 stop next-app || true  # Stop any existing app
                        pm2 start npm --name "next-app" -- run start  # Start the app using PM2
                        pm2 save  # Save the PM2 process list
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
