pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
    }

    environment {
        IMAGE = "vishal1326/springboot-cicd"
        KUBECONFIG = "/var/lib/jenkins/kubeconfig"
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
                """
            }
        }

        stage( 'Trivy Scan') {
            steps {
                sh 'trivy image vishal1326/springboot-cicd:${BUILD_NUMBER}'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'U', passwordVariable: 'P')]) {

                    sh """
                    echo \$P | docker login -u \$U --password-stdin
                    docker push $IMAGE:${BUILD_NUMBER}
                    """
                }
            }
        }

        stage( 'Deploy to Kubernetes' ) {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
