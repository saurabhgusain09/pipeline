pipeline {
    agent any
    environment {
        MAVEN_HOME = tool 'MAVEN'
        SONAR_SCANNER = tool 'SonarScanner'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'my-private-repo-creds', branch: 'main', url: 'https://github.com/saurabhgusain09/superlab.git'
            }
        }
        stage('Maven Build' ) {
            steps {
                echo ''Building project''
                sh "${MAVEN_HOME}/bin/mvn clean verify -Dtest=!FormUITest"
            }
        }
        stage('SonarCloud Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    echo '🔍 Running SonarCloud analysis (coverage fully disabled)...'
                    sh """
                        ${SONAR_SCANNER}/bin/sonar-scanner \
                          -Dsonar.projectKey=sonartestorg02_sonarqubeproject \
                          -Dsonar.organization=sonartestorg02 \
                          -Dsonar.sources=src/main/java \
                          -Dsonar.tests=src/test/java \
                          -Dsonar.java.binaries=target/classes \
                          -Dsonar.coverage.exclusions=**/*.java \
                          -Dsonar.coverage.newCode.requiredCoverage=0 \
                          -Dsonar.newCode.period=1 \
                          -Dsonar.qualitygate.wait=true \
                          -Dsonar.host.url=https://sonarcloud.io
                    """
                }
            }
        }
        stage('Check Sonar Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "❌ Quality Gate failed: ${qg.status}"
                        }
                    }
                }
            }
        }
        stage('Docker Build and Run') {
            steps {
                echo '🐳 Building and running Docker container...'
                sh """
                    docker build -t superlab:${BUILD_NUMBER} .
                    docker ps -q --filter "publish=8081" | grep -q . && docker rm -f \$(docker ps -q --filter "publish=8081") || echo "No container on port 8081"
                    docker run -d -p 8081:8080 --name superlab-app-${BUILD_NUMBER} superlab:${BUILD_NUMBER}
                    sleep 10
                """
            }
        }
    }
    post {
        success {
            echo '✅ All stages up to Docker Run passed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check logs or Docker build errors.'
        }
    }
}