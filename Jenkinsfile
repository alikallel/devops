pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "alikallel/mon-app:latest"
    }

    stages {

        stage('Cloner le dépôt') {
            steps {
                git branch: 'main', url: 'https://github.com/alikallel/devops.git'
            }
        }

        stage('Construire image Docker') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Pousser image Docker') {
            steps {
                withCredentials([
                   usernamePassword(
                      credentialsId: 'dockerhub-creds',
                      usernameVariable: 'DOCKER_USERNAME',
                      passwordVariable: 'DOCKER_PASSWORD'
                   )
                ]) {
                    sh """
                        echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin
                        docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }

        stage('Déployer sur Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh """
                        kubectl --kubeconfig=\$KUBECONFIG apply -f deployment.yaml
                        kubectl --kubeconfig=\$KUBECONFIG apply -f service.yaml
                    """
                }
            }
        }
    }
}
