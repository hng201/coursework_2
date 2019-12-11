pipeline {
	agent any
	tools {nodejs "node"}
	stages {
		stage('Clone Repository') {
			steps {
				//Checks that the git repo has cloned to workspace
				checkout scm
			}
		
		}
		stage('Build') {
			steps {
				sh "export PATH=/usr/local/bin:$PATH"
				sh "node server.js"
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
