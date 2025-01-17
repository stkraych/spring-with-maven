pipeline {

    agent {
        label 'mygroupagent'
    }

    tools{
        maven 'maven'
        dockerTool 'docker'
    }
    triggers{
        githubPush()
    }

   environment {
      DOCKERHUB_CREDENTIALS = credentials('docker_id')
      IMAGE_NAME = 'stkraych/spring-with-maven'
    }

    stages {

        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/stkraych/spring-with-maven.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install' 
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
       

       stage('Docker login'){
           steps {
               sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
	   }
        }

        stage('Docker build and tag'){
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }

	stage('Docker Push'){
            steps {
              sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
    stage('Docker Deploy'){
        steps{
             sh 'docker run -d -p 4000:8080 ${IMAGE_NAME}:${BUILD_NUMBER}'
            // sh 'docker container rm -f spring-with-maven || true'
            // sh 'docker container run -d -p 4000:3000 --name spring-with-maven stkraych/spring-with-maven'
        }
    }

    stage('Clean') {
            steps {
                cleanWs()
            }
     }
          
    
   
 }
}