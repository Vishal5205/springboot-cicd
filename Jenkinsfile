pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'   
    }

    environment {
        IMAGE = "vishal1326/springboot-cicd"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build -t $IMAGE:${BUILD_NUMBER} .
                docker tag $IMAGE:${BUILD_NUMBER} $IMAGE:latest
                """
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'U', passwordVariable: 'P')]) {

                    sh """
                    echo \$P | docker login -u \$U --password-stdin
                    docker push $IMAGE:${BUILD_NUMBER}
                    docker push $IMAGE:latest
                    """
                }
            }
        }
    }
}
