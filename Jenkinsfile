pipeline {
	  environment {
		registry = "nairsreenesh/devopscapstone"
		registryCredential = 'dockerhub'
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
		      
				script {
				  docker build -t registry .
				}
              }
         }		 
		}
	}
