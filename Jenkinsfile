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

        stage('Building project') {
            steps {
                echo '🔧 Building project & running unit tests (excluding Selenium GUI tests)...'
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
                sh """
                    docker build -t superlab:${BUILD_NUMBER} .
                    docker ps -q --filter "publish=8081" | grep -q . && docker rm -f \$(docker ps -q --filter "publish=8081") || echo "No container on port 8081"
                    docker run -d -p 8081:8080 --name superlab-app-${BUILD_NUMBER} superlab:${BUILD_NUMBER}
                    sleep 10
                """
            }
        }

        stage('Selenium Headless GUI Test') {
            steps {
                echo '🚀 Running Selenium GUI tests...'
                sh "${MAVEN_HOME}/bin/mvn -Dtest=FormUITest test -DfailIfNoTests=false"
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo '📦 Pushing image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag superlab1:${BUILD_NUMBER} saurabhgusain21/superlab1:${BUILD_NUMBER}
                        docker push saurabhgusain21/superlab1:${BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ All stages passed successfully and image pushed to Docker Hub!'
        }
        failure {
            echo '❌ Pipeline failed. Please check the logs.'
        }
    }
}