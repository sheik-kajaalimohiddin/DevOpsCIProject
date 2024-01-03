pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
	environment {
		    APP_NAME = "dockerCIProject-pipeline"
			RELEASE = "1.0.0"
			DOCKER_USER = "khaja359"
			DOCKER_PASS = "jenkins-dockerhub-token"
			IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
			IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	}
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'master', credentialsId: 'github_creds', url: 'https://github.com/sheik-kajaalimohiddin/DevOpsCIProject'
                }
        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
	   
	   stage("SonarQube Analysis"){
		   steps {
			   script {
				    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
				    sh "mvn sonar:sonar"
				   }
				}
			}
		}
		
	   stage("Quality Gate"){
		   steps {
			   script {
				    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
				}
			}
		}
		
	   stage("Build and Push docker image"){
		   steps {
			   script {
				   docker.withRegistry('',DOCKER_PASS) {
				       docker_image = docker.build "${IMAGE_NAME}"
				   }
				   
				   docker.withRegistry('',DOCKER_PASS) {
					   docker_image.push("${IMAGE_TAG}")
					   docker_image.push("latest")	
				   }
				}
			}
		}			
   }
}
