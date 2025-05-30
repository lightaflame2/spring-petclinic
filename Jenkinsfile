pipeline {
    agent any
    
    tools {
        maven "M3"
        jdk "JDK21"
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerCredential')
        REGION = "ap-northeast-2"
        AWS_CREDENTIALS_NAME = "AWSCredentials"
    }
    
    stages {
        stage('Git Clone') {
            steps {
                echo 'Git Clone'
                git url: 'https://github.com/lightaflame2/spring-petclinic.git',
                    branch: 'main'
            }
            post {
                success {
                    echo 'Git Clone Success'
                }
                failure {
                    echo 'Git Clone Failure'
                }
            }
        }
        // Maven Build 작업 
        stage('Maven Build') {
            steps {
                echo 'Maven Build'
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'  
            }
        }

        // Docker Image 생성
        stage('Docker Image Build') {
            steps {
                echo 'Docker Image Build'
                dir("${env.WORKSPACE}") {
                    sh '''
                       docker build -t spring-petclinic:$BUILD_NUMBER .
                       docker tag spring-petclinic:$BUILD_NUMBER lightaflame/spring-petclinic:latest
                       '''

                }
            }
        }

        // Docker Image Push
        stage('Docker Image Push') {
            steps {
                sh '''
                   echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                   docker push lightaflame/spring-petclinic:latest
                   '''
            }
        }

        // Remove Docker Image
        stage('Remove Docker Image') { 
            steps {
                sh '''
                docker rmi spring-petclinic:$BUILD_NUMBER
                docker rmi lightaflame/spring-petclinic:latest
                '''
            
            }
        }
    }
}
