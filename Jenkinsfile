pipeline {
    agent any
    }
    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'my-private-repo-creds', branch: 'main', url: 'https://github.com/saurabhgusain09/superlab.git'
            }
        }
    }
    post {
        success {
            echo '✅ Code checkout completed successfully!'
        }
        failure {
            echo '❌ Code checkout failed. Please check the logs.'
        }
    }
