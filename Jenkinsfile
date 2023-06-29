pipeline {
    agent any
   
	environment {
		registry = "snehalhhande/trialjenkins"
		img = "$registry" + ":${env.BUILD_ID}"
		registryCredential = 'docker-hub-login' 
    }	

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/snehalhhande/trialjenkins.git'
                sh 'ls -la'
            }
        }

        stage('Stop running Container') {
			steps{
                echo "${JOB_NAME}"
					sh returnStatus: true, script: 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
					sh returnStatus: true, script: 'docker rmi $(docker images | grep ${registry} | awk \'{print $3}\') --force' 
					sh returnStatus: true, script: 'docker rm ${JOB_NAME}'
				}	
		}
		
        stage('Build') {
            steps {
				echo "Building our image"
				script {
					sh "docker build -f /Hospital_Appointment_System/Hospital_Appointment_Booking_System/Dockerfile -t ${img} ."
                }
            }
        }

        stage('Run') {
			steps{
				echo "Run image"
				sh returnStdout: true, script: "docker run --rm -d --name ${JOB_NAME} -p 8082:5000 ${img}"
			}
		}

        stage('Release') {
            steps {
				script {
					echo "Push to docker hub"
                    docker.withRegistry( 'https://registry.hub.docker.com ', registryCredential )  {
							dockerImg.push()
							//dockerImg.push('latest') //one more push for latest tag
						}
                }
            }
        }
		
          
      
    }
}