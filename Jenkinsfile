pipeline {
    agent any

   

    stages {
        stage('Stage 1 - Name Here') {
            steps {
                echo "🛠️ Step description here"
                // Commands here (e.g., git, sh, etc.)
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