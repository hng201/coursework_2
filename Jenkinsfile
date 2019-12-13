def remote= [:]
        remote.name = 'coursework2-node'
        remote.host = 'azure'
        remote.user = 'azureuser'
        remote.password = ''
        remote.allowAnyHosts = true

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
				sshCommand remote: remote, command: "sudo kubectl set image deployments/coursework2-deployment coursework2-deployment=hng201/devops-cw:latest", sudo: true 
			}
		}
	}
}
