pipeline {
	agent any
	environment {
		app = ''
	}
	stages {
		stage('Clone Repository') {
			steps {
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
				timeout(time: 10, unit: 'MINUTES') {
         			   waitForQualityGate abortPipeline: true
        			}
			}
		}
		stage('Build Image') {
			steps {
				script {
					app = docker.build("hng201/devops-cw")	
				}
			}
		}
		stage('Test Image') {
			steps {
				script {
					app.inside {
						sh "echo 'Image is running successfully'"
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
	        stage('Update Image') {
			steps {
				sh "ssh azureuser@40.78.135.135 'kubectl set image deployments/coursework2-deployment coursework2-deployement=hng201/devops-cw:latest'" 
			}
		}
	}
}
