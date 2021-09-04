pipeline {
    agent any
    tools {
        maven 'maven3.8'
        jdk 'jdk8'
    }
    environment { 
        AWS_REGION = 'us-west-2'
        ECRREGISTRY = '007600611043.dkr.ecr.us-west-2.amazonaws.com'
        IMAGENAME = 'demomk'
        IMAGE_TAG = 'latest'
    }
    stages {
       stage ('Clone') {
          steps {
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'mvn surefire:test'
            }
        }
        stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonarserver') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }

         stage('Deployment Approval') {
            steps {
              script {
                timeout(time: 10, unit: 'MINUTES') {
                 input(id: 'Deploy Gate', message: 'Deploy Application to Dev ?', ok: 'Deploy')
                 }
               }
            }
         }   
        
         stage('AWS ecr login') {
            steps {
                sh 'aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECRREGISTRY}'
            }
        }        
         stage('docker build and tag') {
            steps {
                sh 'docker build -t ${IMAGENAME}:${IMAGE_TAG} .'
                sh 'docker tag ${IMAGENAME}:${IMAGE_TAG} ${ECRREGISTRY}/${IMAGENAME}:${IMAGE_TAG}'
            }
        }  
         stage('docker push') {
            steps {
                sh 'docker push ${ECRREGISTRY}/${IMAGENAME}:${IMAGE_TAG}'
            }
        }                
        
    }
    post {
        always {
            junit 'target/surefire-reports/TEST-*.xml'
            deleteDir()
        }
    }
}
