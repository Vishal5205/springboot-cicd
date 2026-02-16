pipeline {
    agent any

    environment {
        IMAGE = "vishal1326/springboot-cicd:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/Vishal5205/springboot-cicd.git'
            }
        }

        stage('Build Jar') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE
                    '''
                }
            }
        }

        stage('Auto Deploy') {
            steps {
                sh '''
                    docker stop springboot || true
                    docker rm springboot || true
                    docker pull $IMAGE
                    docker run -d --name springboot -p 8090:8080 $IMAGE
                '''
            }
        }
    }
}
