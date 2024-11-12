def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
    ]
pipeline {
    agent any
    tools {
    maven 'maven'
  }
     environment {
        SCANNER_HOME = tool 'sonar-server'
    }
    stages {
        stage('git checkout') {
            steps {
            git 'https://github.com/Yaswanth270/Java-Springboot'
            }
        }
         stage('compile') {
            steps {
              sh 'mvn compile'
            }
        }
         stage('code analysis') {
            steps {
              withSonarQubeEnv('sonar-server') {
               sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Java-Springboot \
               -Dsonar.java.binaries=. \
               -Dsonar.projectKey=Java-Springboot'''
              }
            }
        }
        stage('package') {
            steps {
              sh 'mvn install'
            }
        }
         stage('docker build') {
            steps {
             script {
                 withDockerRegistry(credentialsId: 'docker-key', toolName: 'docker') {
                    sh 'docker build -t java-spring .'
                  }
              }
            }
        }
         stage('docker push') {
            steps {
             script {
                withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                    sh 'docker tag java-spring yaswanth270/java-spring'
                    sh 'docker push yaswanth270/java-spring'
                  }
              }
            }
         }    
        stage('docker container') {
            steps {
             script {
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                    sh 'docker run -itd --name javaspring-cont -p 8085:8085 java-spring'
                  }
              }
            }
        }    
    }	
 
    post {
        always {
            echo 'slack Notification.'
            slackSend channel: '#java-ci-cd-pipeline',
            color: COLOR_MAP [currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URl}"
            
        }
    }
}
