pipeline {
    agent any

    environment {
        IMAGE = "vishal1326/springboot-cicd:latest"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvnw clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE
                    '''
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                sh '''
                    kubectl delete deployment springboot || true
                    kubectl create deployment springboot --image=$IMAGE
                    kubectl expose deployment springboot --type=NodePort --port=80 --target-port=8080 || true
                '''
            }
        }
    }
}
