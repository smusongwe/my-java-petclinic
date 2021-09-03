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
        // Now Maven is intergrating Sonar-scanner for indept and rubost test, code smell, code volnurability
        stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonarserver') {
                sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://18.237.104.114:9000 -Dsonar.login=3deb33db1b478b0d1ea6747539ad69f7e9cc704b -Dsonar.projectKey=jjtech -Dsonr.projectName=Haplet -Dsonar.Version=1.0'
              }
            }
          }
    }
}
