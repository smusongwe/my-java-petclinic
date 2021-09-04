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
         stage("build & SonarQube analysis") {
             agent any
               steps {
                withSonarQubeEnv('sonarserver') {
                sh "mvn clean package sonar:sonar  -Dsonar.host.url=http://54.202.30.104:9000 -Dsonar.login=9d459e9a90f58879082ce0d24f02e929450af88b -Dsonar.projectKey=jjtech -Dsonar.projectName=Haplet -Dsonar.Version=1.0"
              }
            }
          }
       }
    }
}
