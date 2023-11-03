pipeline {

        agent {
                label "aj-slave"
                }
                stages{
                        stage ("Pull the code latest from SCM"){
                                steps {
                                        git branch: 'main', url: 'https://github.com/ajay-devop/automate-jenkins-file.git'
                                        }
                        }
                        stage (" Build the code "){
                                steps {
                                        sh 'sudo mvn dependency:purge-local-repository'
					sh 'sudo mvn clean package'
                                        }
                        }
			stage (" Build a docker Image " ){
			     	steps {
					sh 'sudo docker build -t new-java-app:$BUILD_TAG .'
					sh 'sudo docker tag new-java-app:$BUILD_TAG ajaydevop/new-java-app:$BUILD_TAG'
		
		                        }
			}
			stage (" Push to dockerhub" ){
				steps {
				  withCredentials([usernamePassword(credentialsId: 'docker-hub-pass', passwordVariable: 'docker_pass_var', usernameVariable: 'docker_user_var')]) {
                                        sh 'sudo docker login -u ${docker_user_var} -p ${docker_pass_var}'
					sh 'sudo docker push ajaydevop/new-java-app:$BUILD_TAG'

                                      }


				}	
	
			}
			stage ("testing the pipeline") {
                                steps {
				       
				       sh 'sudo docker run -dit --name new-java-app -p 8080:8080 new-java-app:$BUILD_TAG'
				}
			}
			stage ("show the continer post"){
                               steps {
                                      sh 'sudo docker ps '
			       }
			}
			stage ("testing the website") {
                               steps {
			              retry (5) {
                                                script {
                                                         sh 'curl http://15.206.168.215:8080/java-web-app/ | grep -i -E "india|aj"'      
					         }
				      }
                                    
			       }
			}
			stage ("Approval status") {
                               steps {
                                     input " Please approve to proccess with deployment"
                               }
                        }
			stage ("production EVN") {
			       steps {
			               script {
			                       sshagent(['gcp-by-aws-agent']) {
				                  sh 'ssh ec2-user@43.204.237.82 -o StrictHostKeyChecking=no'
						  
					          sh 'ssh ec2-user@43.204.237.82  sudo docker run -d -p 8080:8080 ajaydevop/new-java-app:$BUILD_TAG'
						  sh 'ssh ec2-user@43.204.237.82 sudo docker ps'
                                          }

				     }


			       }

			}


                }
        }
