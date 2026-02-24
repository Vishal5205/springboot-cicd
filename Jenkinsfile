pipeline {
    agent any

    tools {
        maven 'Maven-3.9.6'
    }

    environment {
        IMAGE = "vishal1326/springboot-cicd"
        GITOPS_REPO = "https://github.com/Vishal5205/springboot-k8s.git"
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

        stage('Update GitOps Repo') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-creds',
                usernameVariable: 'GU', passwordVariable: 'GP')]) {

                    sh """
                    rm -rf springboot-k8s
                    git clone https://\$GU:\$GP@github.com/Vishal5205/springboot-k8s.git
                    cd springboot-k8s

                    sed -i "s|image:.*|image: $IMAGE:${BUILD_NUMBER}|" deployment.yaml

                    git config user.email "jenkins@devops.com"
                    git config user.name "jenkins"

                    git add deployment.yaml
                    git commit -m "Update image to $IMAGE:${BUILD_NUMBER}"
                    git push
                    """
                }
            }
        }
    }
}
