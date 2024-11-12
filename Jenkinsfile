def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]
pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'sonarqube'
    }
    stages {
        stage('git checkout') {
            steps {
                git 'https://github.com/Yaswanth270/Java-Springboot.git'
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
                    sh ''' 
                        $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=CI-CD-Project \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=CI-CD-Project
                    '''
                }
            }
        }
        stage('package') {
            steps {
                sh 'mvn install'
            }
        }
        stage('docker login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Docker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }
        stage('docker build') {
            steps {
                sh 'docker build -t spring-boot .'
            }
        }
        stage('docker push') {
            steps {
                sh 'docker tag spring-boot yaswanth270/abcd'
                sh 'docker push yaswanth270/abcd'
            }
        }
        stage('docker container') {
            steps {
                sh 'docker run -itd -p 8085:8085 spring-boot'
            }
        }
    }

    post {
        always {
            echo 'Slack Notification.'
            slackSend channel: '#java-ci-cd-pipeline',
                      color: COLOR_MAP[currentBuild.currentResult],
                      message: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
