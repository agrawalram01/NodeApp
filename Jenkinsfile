Pipeline{
	agent any
	environment{
		DOCKER_TAG = getDockerTag()
	}
	stages{
		stage("Git Checkout"){
			steps{
				git credentialsId: 'github', url: 'https://github.com/agrawalram01/NodeApp.git' #script generated using jenkins pipeline syntax
			}
		}
		stage("Code Build"){
			steps{
				sh "npm install" #use mvn clean package to build and generate war file
			}
		}
	}
}



def getDockerTag(){
	def tag = sh script: 'git rev-parse HEAD', returnStdout: true
	return tag
}
