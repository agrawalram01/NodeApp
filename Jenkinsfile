pipeline{
	agent any
	environment{
		DOCKER_TAG = getDockerTag()
	}
	stages{
		stage("Git Checkout"){
			steps{
				git credentialsId: 'github', url: 'https://github.com/agrawalram01/NodeApp.git' 
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
		stage("Deploy to k8s"){
			steps{
				sh "cd /home/ubuntu/test/NodeApp/"
				sh "chmod +x changeTag.sh"
				sh "./changeTag.sh S{DOCKER_TAG}"
				sshagent(["k8s-machine"]){
					sh "scp -o StrictHostKeyChecking=no service.yml app-name-pod.yml ubuntu@3.137.158.221:/home/ubuntu/"
					script{
						try{
							sh "ssh ubuntu@3.137.158.221 kubectl apply -f ."
						}catch(error){
							sh "ssh ubuntu@3.137.158.221 kubectl create -f ."
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
