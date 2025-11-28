pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/ac-itsolutions2025/devsecops-jenkins-k8s-tf-sast-sonarcloud-repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SAST Scan') {
            steps {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=acitbuggywebapp -Dsonar.organization=acitbuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=ca46f9dc55bc160e996cab8821b2ba1cd0a77495'
            }
        }
    }
}
