pipeline {
	agent any
	stages {
		stage('Clone Repository') {
			steps {
				//Checks that the git repo has cloned to workspace
				checkout scm
			}
		}
		stage('SonarQube Test') {
			environment {
				scannerHome = tool 'SonarQubeScanner'
			}
			steps {
				withSonarQubeEnv('sonarqube') {
					sh "{scannerHome}/bin/sonar-scanner"
				}
				
				//Wait for 10 minutes before aborting build
				//if the build does not complete
				timeout(time: 10, unit: 'MINUTES') {
					waitForQualityGate abortPipeline: true
				}
			}
		}
	}
}
