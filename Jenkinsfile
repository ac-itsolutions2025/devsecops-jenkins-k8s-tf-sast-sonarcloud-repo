pipeline {
    agent any
    
    environment {
        SONAR_URL = 'https://sonarcloud.io/'
        SONAR_ORG = 'acitbuggywebapp'
        SONAR_PROJECT_KEY = 'acitbuggywebapp'
        SONAR_TOKEN = credentials('sonarcloud-token') // Store token as Jenkins credential
    }
    
    tools {
        maven 'Maven-3.8.4' // Configure Maven tool in Jenkins Global Tool Configuration
        jdk 'JDK-17' 
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
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('SAST Scan') {
            steps {
                echo 'Running SonarCloud SAST scan...'
                withSonarQubeEnv('SonarCloud') {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.host.url=${SONAR_URL} \
                        -Dsonar.organization=${SONAR_ORG} \
                        -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                        -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                echo 'Checking SonarCloud Quality Gate...'
                timeout(time: 5, unit: 'MINUTES') {
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
