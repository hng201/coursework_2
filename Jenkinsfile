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
				sh "apk add nodejs"
				withSonarQubeEnv('sonarqube') {					
					sh "${scannerHome}/bin/sonar-scanner"	
				}
			}
		}
		stage('Build Image') {
			steps {
				script {
					docker.build("coursework_2/cw2-node")	
				}
			}
		}
		stage('Test Image') {
			environment {
				app = docker.build("coursework_2/cw2-node")
			}
			steps {
				script {
					app.inside {
						sh "echo 'Image is running successfully"
					}
				}
			}
		}	
		stage('Push Image') {
			steps {
				script {
					docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
						app.push("${env.BUILD_NUMBER}")
						app.push("latest")
					}
				}
			}
		}
	}
}
