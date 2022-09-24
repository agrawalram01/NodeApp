pipeline{
	agent any
	environment{
		DOCKER_TAG = getDockerTag()
	}
	stages{
		stage("Git Checkout"){
			steps{
				git branch: 'main', credentialsId: 'git-hub', url: 'https://github.com/agrawalram01/NodeApp.git'
			}
		}
		stage("Code Build"){
			steps{
				sh "npm install" 
			}
		}
		stage("Build docker image"){
			steps{
				script{
					sh "docker build . -t agrawalram/nodeapp:${DOCKER_TAG}"
				}
			}
		}
		stage("Push image to Docker Hub"){
			steps{
				withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]){
					sh "docker login -u agrawalram -p ${dockerHubPwd}"
					sh "docker push agrawalram/nodeapp:${DOCKER_TAG}"
				}
			}
		}
		stage("Create required file to deploy"){
			steps{
				sh "pwd"	    
			}
		}
		
		stage("Deploy to k8s"){
			steps{
				//sh "sudo cp /home/ubuntu/test/NodeApp/pods.yml services.yml changeTag.sh ."
				//sh "chown -R jenkins:jenkins ."
				//sh "chmod +x changeTag.sh"
				//sh "sed "s/tagVersion/$1/g" pods.yml > node-app-pod.yml"
				
				sh "chown -R jenkins:jenkins ."
				sshagent(["k8s-machine"]){
					sh "scp -o StrictHostKeyChecking=no services.yml deployments.yml ubuntu@3.144.229.221:/home/ubuntu/"
					script{
						try{
							sh "ssh ubuntu@3.144.229.221 kubectl apply -f ."
						}catch(error){
							sh "ssh ubuntu@3.144.229.221 kubectl create -f ."
						}
					}
				}
			}
		}
	}
}



def getDockerTag(){
	def tag = sh script: 'git rev-parse HEAD', returnStdout: true
	return tag
}
