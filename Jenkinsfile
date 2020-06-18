pipeline {
	  environment {
		registry = "nairsreenesh/devopscapstone"
		registryCredential = 'dockerhub'
		dockerImage=''
	  }
     agent any
     stages {
         stage('install dependencies') {
              steps {
                  sh 'python3 -m venv venv'
                  sh '. venv/bin/activate'
                  sh 'make install'
				  sh 'echo "Install dependencies complete"'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
				  sh 'echo "Linting complete"'
              }
         }	     
         stage('Building image') {
              steps {
		      sh 'sudo usermod -aG docker ${USER}'
		      sh 'sudo docker build -t $registry:$BUILD_ID .'
		      
				  }
         }
		stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						sudo docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
						sudo docker push $registry:$BUILD_ID
					'''
				}
			}
		}

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-static') {
					sh '''
						kubectl config use-context arn:aws:eks:us-west-2:523896841463:cluster/capstonecluster
					'''
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-static') {
					sh '''
						kubectl apply -f ./blue-controller.json
					'''
				}
			}
		}

		stage('Deploy green container') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-static') {
					sh '''
						kubectl apply -f ./green-controller.json
					'''
				}
			}
		}

		stage('Create the service in the cluster, redirect to blue') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-static') {
					sh '''
						kubectl apply -f ./blue-service.json
					'''
				}
			}
		}

		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }

		stage('Create the service in the cluster, redirect to green') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-static') {
					sh '''
						kubectl apply -f ./green-service.json
					'''
				}
			}
		}	     
	     
	}
}
