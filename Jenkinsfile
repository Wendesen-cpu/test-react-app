pipeline {
    agent any
    

    environment {
        // Set any global environment variables here, if needed
        NODE_HOME = '/usr/local/bin/node' // Path to Node.js installation
        NPM_HOME = '/usr/local/bin/npm'   // Path to npm installation
    }

    stages {

        stage('Setup Node.js') {
    steps {
        script {
            sh '''
                export NVM_DIR="$HOME/.nvm"
                [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                nvm install 18  # Replace with your Node.js version
                nvm use 18
                node -v
                npm -v
            '''
        }
    }
}

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

        stage('Deploy') {
            steps {
                // Deploy your app (this example is for Vercel, change as needed)
                script {
                    // Example deployment (you could use different services like AWS, Netlify, etc.)
                    // For Vercel, you'd usually deploy by running `vercel` CLI with environment variables
                    // or automate this step depending on your environment.
                    sh 'vercel --prod' // You can replace this with your actual deploy command.
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
