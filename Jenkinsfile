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



      stage('Deploy to EC2') {
             steps {
                script {
                    echo "Deploying to EC2 (excluding node_modules)..."
                    sh '''
                    # Copy the entire project directory to the EC2 instance, excluding node_modules
                    rsync -avz --exclude node_modules/ . $EC2_USER@$EC2_HOST:$APP_DIR
                    '''
                }
            }
        }

        stage('Install Dependencies and Build on EC2') {
            steps {
                script {
                    echo "Installing dependencies and building on EC2..."
                    sh '''
                    ssh -i $SSH_KEY $EC2_USER@$EC2_HOST << EOF
                        # Navigate to the app directory on the EC2 instance
                        cd $APP_DIR

                        # Install the dependencies
                        npm install

                        # Build the Next.js app
                        npm run build
                    EOF
                    '''
                }
            }
        }

        stage('Start Application on EC2') {
            steps {
                script {
                    echo "Starting the application on EC2..."
                    sh '''
                    ssh -i $SSH_KEY $EC2_USER@$EC2_HOST << EOF
                        cd $APP_DIR

                        # Install production dependencies
                        npm install --omit=dev

                        # Start the app with pm2
                        pm2 start npm --name "next-app" -- run start
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
