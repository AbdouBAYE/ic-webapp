/* import shared library */
@Library('shared-library')_

pipeline {
     environment {
       ID_DOCKER = "abdoulayedevops"
       IMAGE_NAME = "ic-webapp"
       IMAGE_TAG = "v1"
       INTERNAL_PORT = "8080"
       EXTERNAL_PORT = "8080"
       CONTAINER_IMAGE = "${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG}"

     }
     agent none
     stages {
         stage('Build image') {
             agent any
             steps {
                script {
                  sh 'docker build -t ${ID_DOCKER}/$IMAGE_NAME:$IMAGE_TAG -f ./Docker-ressources/Dockerfile'
                }
             }
        }
          
      stage('Scan Image with  SNYK') {
            agent any
            environment{
                SNYK_TOKEN = credentials('SNYK_TOKEN')
            }
            steps {
                script{
                    sh '''
                    echo "Starting Image scan ${ID_DOCKER}/$IMAGE_NAME:$IMAGE_TAG ..." 
                    echo There is Scan result : 
                    SCAN_RESULT=$(docker run --rm -e SNYK_TOKEN=$SNYK_TOKEN -v /var/run/docker.sock:/var/run/docker.sock -v $(pwd):/app snyk/snyk:docker snyk test --docker $ID_DOCKER/$IMAGE_NAME:$IMAGE_TAG --json ||  if [[ $? -gt "1" ]];then echo -e "Warning, you must see scan result \n" ;  false; elif [[ $? -eq "0" ]]; then   echo "PASS : Nothing to Do"; elif [[ $? -eq "1" ]]; then   echo "Warning, passing with something to do";  else false; fi)
                    echo "Scan ended"
                    '''
                }
            }
        }          
       
        stage('Run container based on builded image') {
            agent any
            steps {
               script {
                 sh '''
                    echo "Cleaning existing container if exist"
                    docker ps -a | grep -i $IMAGE_NAME && docker rm -f ${IMAGE_NAME}
                    docker run --name $IMAGE_NAME -d -p ${EXTERNAL_PORT}:${INTERNAL_PORT} ${ID_DOCKER}/$IMAGE_NAME:$IMAGE_TAG
                    sleep 5
                 '''
               }
            }
       }
       
       stage('Test image') {
           agent any
           steps {
              script {
                sh '''
                    curl http://172.17.0.1:${EXTERNAL_PORT} | grep -i "200"
                '''
              }
           }
      }
       
      stage('Clean Container') {
          agent any
          steps {
             script {
               sh '''
                 docker stop $IMAGE_NAME
                 docker rm $IMAGE_NAME
               '''
             }
          }
     }

      stage('Save Artefact') {
          agent any
          steps {
             script {
               sh '''
                 docker save  ${ID_DOCKER}/$IMAGE_NAME:$IMAGE_TAG > /tmp/ic-webapp.tar                 
               '''
             }
          }
     }          
          
     stage ('Login and Push Image on docker hub') {
        agent any
        environment {
           DOCKERHUB_PASSWORD  = credentials('dockerhub_abdoulayedevops')
        }            
          steps {
             script {
               sh '''
                   echo $DOCKERHUB_PASSWORD_PSW | docker login -u $ID_DOCKER --password-stdin
                   docker push ${ID_DOCKER}/$IMAGE_NAME:$IMAGE_TAG
               '''
             }
          }
      }               
    }       
     
  post {
       always {
       script {
         slackNotifier currentBuild.result
     }
    }
   }
  }
