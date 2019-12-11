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
				sh "node server.js || exit 1"
			}
		}
		stage('SonarQube Test') {
			environment {
				scannerHome = tool 'SonarQubeScanner'
			}
			steps {
				sh "apk add nodejs"
				withSonarQubeEnv('sonarqube') {					
					sh "${scannerHome}/bin/sonar-scanner"	
				}
			}
		}
		stage('Build Image') {
			steps {
				app = docker.build("coursework_2/cw2-node")	
			}
		}
		stage('Test Image') {
			steps {
				app.inside {
					sh "node server.js || exit 1"
				}
			}
		}	
		stage('Push Image' {
			steps {
				docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
					app.push("${env.BUILD_NUMBER}")
					app.push("latest")
				}
			}
		}
	}
}
