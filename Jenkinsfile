pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
    }

    environment {
        IMAGE = "vishal1326/springboot-cicd"
        GITOPS_REPO = "https://github.com/Vishal5205/springboot-cicd.git"
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
