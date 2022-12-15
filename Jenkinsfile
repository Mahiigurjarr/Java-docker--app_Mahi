pipeline {
	agent {	
		label 'Slave_ubuntu_1'
		}
	stages {
		stage("SCM") {
			steps {
				git branch: 'main', url: 'https://github.com/Mahiigurjarr/Java-docker--app_Mahi.git'
				}
			}

		stage("build") {
			steps {
				sh 'sudo mvn dependency:purge-local-repository'
				sh 'sudo mvn clean package'
				}
			}
		stage("Image") {
			steps {
				sh 'sudo docker build -t java-repo:$BUILD_TAG . '
				sh 'sudo docker tag java-repo:$BUILD_TAG mahigurjarr/pipeline-java:$BUILD_TAG'
				}
			}
				
	
		stage("Docker Hub") {
			steps {
			withCredentials([string(credentialsId: 'Docker_hub_mahi', variable: 'docker_hub_password_var')]) {
				sh 'sudo docker login -u mahigurjarr -p ${docker_hub_password_var}'
				sh 'sudo docker push mahigurjarr/pipeline-java:$BUILD_TAG'
				}
			}	

		}
		stage("QAT Testing") {
			steps { 
				sh 'sudo docker rm -f $(sudo docker ps -a -q)'
				sh 'sudo docker run -dit -p 8080:8080 --name web11 mahigurjarr/pipeline-java:$BUILD_TAG'
			}
		}
		stage('QAT testing') {
			steps {
			 retry(5) {
			    sh 'curl --silent http://13.214.186.134:8080/java-web-app/|grep -iE "mahi|devops"'
			 }
			}
		}

		stage("Approval status") {
			steps {
				script {  
               	 Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
               	 echo 'userInput: ' + userInput
				}
	 		}
		}
	}
}

