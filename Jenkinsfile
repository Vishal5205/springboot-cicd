pipeline {
    agent any

    environment {
        IMAGE_NAME = "vishal1326/springboot-cicd"
        DOCKER_CREDS = "dockerhub-creds"
        JAVA_HOME = "/usr/lib/jvm/java-21-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {

        stage('Build Maven') {
            steps {
                sh '''
                java -version
                mvn -version
                mvn clean package -DskipTests
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:${BUILD_NUMBER} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDS,
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {

                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME:${BUILD_NUMBER}
                    docker tag $IMAGE_NAME:${BUILD_NUMBER} $IMAGE_NAME:latest
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }
    }
}
