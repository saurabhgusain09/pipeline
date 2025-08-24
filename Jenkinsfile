pipeline {
    agent any

   

    stages {
        stage('Stage 1 -Checkout code') {
            steps {
                echo "🔁 Cloning Private GitHub Repository.."
                git credentialsId: 'my-private-repo-creds', branch: 'main', url: 'https://github.com/saurabhgusain09/superlab.git'

            }
        }

        stage('Stage 2 - Name Here') {
            steps {
                echo "🔍 Step description here"
                // Commands here
            }
        }

        // ➕ Add more stages as needed
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check the logs."
        }
    }
}