def remote = [:]
    remote.name = 'slave1.ansible'
    remote.host = 'slave1.ansible'
    remote.user = 'root'
    remote.password = 'Dario.01!'
    remote.allowAnyHosts = true

pipeline{
    
	agent any
    stages {
    		stage('Git Clone') {
    			steps {
    				dir("hello-world") {
    					script {
    						git url: "https://github.com/strama89/ExampleContainer.git",
    						credentialsId: "personalCredGitHub"
    					}
    				}
    			}
    		}
    		stage('Create the docker image') {
    		    steps{
    		        dir("hello-world") {
    		            sh 'docker build -t example/example-container .'
    		        }
    		    }
    		}
    		stage('Save Image to transfer on remote server') {
    		    steps{
    		        dir("save-images") {
    		            sh 'docker save example/example-container > example-container.tar'
    		        }
    		    }
    		}
    		stage('Transfer file to Slave 1') {
    			steps {
    				dir("save-images") {
					sh 'sshpass -p "qwerty" scp -v -o StrictHostKeyChecking=no example-container.tar jenkins@slave1.ansible:/tmp/'
							   }				
					}
			}
			stage('Load docker image on remote host') {
			steps {
                sshCommand remote: remote, command: "docker load < /tmp/example-container.tar"
				}
            }
			
			stage('Run docker image loaded in the previous stage') {
			steps {
                sshCommand remote: remote, command: "docker run --rm -d -p 5000:5000 example/example-container"
				}
            }
    	}
}
