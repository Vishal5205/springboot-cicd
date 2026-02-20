pipeline {
    agent any

    environment {
        IMAGE = "vishal1326/springboot-cicd:latest"
    }

    stages {

        stage('Build Jar') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                      echo $PASS | docker login -u $USER --password-stdin
                      docker push $IMAGE
                    '''
                }
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                  docker stop springboot || true
                  docker rm springboot || true
                  docker run -d --name springboot -p 8090:8080 $IMAGE
                '''
            }
        }
    }
}
