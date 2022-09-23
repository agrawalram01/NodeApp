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
		stage("Create required file to deploy"){
			steps{
				    writeFile file: 'services.yml', text: 'Working with files the Groovy way is easy.'
				    sh 'ls -l services.yml'
				    sh 'cat services.yml'
			}
		}
		
		stage("Deploy to k8s"){
			steps{
				//sh "sudo cp /home/ubuntu/test/NodeApp/pods.yml services.yml changeTag.sh ."
				//sh "chown -R jenkins:jenkins ."
				//sh "chmod +x changeTag.sh"
				//sh "sed "s/tagVersion/$1/g" pods.yml > node-app-pod.yml"
				writeFile file: 'deployment.yml', text: '''apiVersion: apps/v1
kind: Deployment
metadata:
	name: nodeapp
	app: nodeapp
spec:
	selector:
		matchLabels:
		app: nodeapp
	replicas: 2  
	template:
		metadata:
			labels:
				app: nodeapp
	spec:
	containers:
		- name: nodeapp
		  image: agrawalram/nodeapp:tagVersion
		  ports:
			- containerPort: 80'''
				sh "chown -R jenkins:jenkins ."
				sshagent(["k8s-machine"]){
					sh "scp -o StrictHostKeyChecking=no services.yml pods.yml ubuntu@3.144.229.221:/home/ubuntu/"
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
