pipeline {
  agent any
  tools { 
        maven 'Maven_3_8_4'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=acitbuggywebapp -Dsonar.organization=acitbuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=ca46f9dc55bc160e996cab8821b2ba1cd0a77495'
			}
        } 
  }
}
