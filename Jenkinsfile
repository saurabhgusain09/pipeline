pipeline {
    agent any
    environment {
    MAVEN_HOME = tool 'MAVEN' // Use the name given in Global Tool Configuration
}


   

    stages {
        stage('Stage 1 -Checkout code') {
            steps {
                echo "🔁 Cloning Private GitHub Repository.."
                git credentialsId: 'my-private-repo-creds', branch: 'main', url: 'https://github.com/saurabhgusain09/superlab.git'

            }
        }

        stage('Stage 2 - Maven Build') {
            steps {
                echo "Building Project"
                sh "${MAVEN_HOME}/bin/mvn clean verify -Dtest=!FormUITest"
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