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
    						git url: "https://github.com/strama89/CI-helloworld.git",
    						credentialsId: "personalCredGitHub"
    					}
    				}
    			}
    		}
    		stage('Create the docker image') {
    		    steps{
    		        dir("hello-world") {
    		            sh 'docker build -t example/hello-world2 .'
    		        }
    		    }
    		}
    		stage('Save Image to transfer on remote server') {
    		    steps{
    		        dir("save-images") {
    		            sh 'docker save example/hello-world2 > hello-world2.tar'
    		        }
    		    }
    		}
    		stage('Transfer file to Slave 1') {
    			steps {
    				dir("save-images") {
								sh 'sshpass -p "qwerty" scp -v -o StrictHostKeyChecking=no hello-world2.tar jenkins@slave1.ansible:/tmp/'
							   }				
					}
			}
			stage('Remote SSH') {
			steps {
                sshCommand remote: remote, command: "docker load < /tmp/hello-world2.tar"
				}
            }
    	}
}
