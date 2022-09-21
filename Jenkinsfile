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
	}
}



def getDockerTag(){
	def tag = sh script: 'git rev-parse HEAD', returnStdout: true
	return tag
}
