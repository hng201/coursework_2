pipeline {
	agent any
	stages {
		stage('Clone Repository') {
			steps {
				//Checks that the git repo has cloned to workspace
				checkout scm
			}
		
		}
		stage('Build') {
			steps {
				sh "apk add nodejs"
				sh "node server.js"
				sh "^C"
			}
		}
		stage('SonarQube Test') {
			environment {
				scannerHome = tool 'SonarQubeScanner'
			}
			steps {
						withSonarQubeEnv('sonarqube') {
						sh "${scannerHome}/bin/sonar-scanner"
					}
				
				}
			}
	}
}
