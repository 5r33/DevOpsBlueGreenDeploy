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
		 stage('Deploy Image') {
			  steps{
				 script {
					 sudo docker.withRegistry( '', registryCredential ) {
						sudo docker push $registry:$BUILD_ID }
				}
			  }
		}			 
	}
}
