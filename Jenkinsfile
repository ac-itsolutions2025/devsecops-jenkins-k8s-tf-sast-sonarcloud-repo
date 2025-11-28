pipeline {
    agent any

    tools {
        // Make sure this name matches the Maven installation in
        // "Manage Jenkins" → "Global Tool Configuration"
        maven 'Maven_3.8.4'
    }

    environment {
        // SonarCloud organization and project key
        SONAR_ORG         = 'acitbuggywebapp'
        SONAR_PROJECT_KEY = 'acitbuggywebapp'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                git branch: 'main',
                    url: 'https://github.com/ac-itsolutions2025/devsecops-jenkins-k8s-tf-sast-sonarcloud-repo.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building Java application...'
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
            post {
                always {
                    // Publish JUnit test reports
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SAST Scan') {
            steps {
                echo 'Running SonarCloud SAST scan...'

                // "SonarCloud" must match the name of the server in:
                // Manage Jenkins → Configure System → SonarQube servers
                withSonarQubeEnv('SonarCloud') {
                    sh """
                        mvn sonar:sonar \
                          -Dsonar.host.url=$SONAR_HOST_URL \
                          -Dsonar.organization=$SONAR_ORG \
                          -Dsonar.projectKey=$SONAR_PROJECT_KEY \
                          -Dsonar.token=$SONAR_AUTH_TOKEN
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo 'Checking SonarCloud Quality Gate...'
                timeout(time: 5, unit: 'MINUTES') {
                    // Fails the pipeline if the Quality Gate is not passed
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
        always {
            cleanWs()
        }
    }
}
