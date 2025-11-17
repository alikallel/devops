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
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', 
                        usernameVariable: 'DOCKER_USERNAME', 
                        passwordVariable: 'DOCKER_PASSWORD')]) {

                        sh 'docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"'

                        sh 'docker build -t alikallel/mon-app:latest .'
                    }
                }
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
                sh """
                    kubectl --kubeconfig=/var/jenkins_home/.kube/config apply -f deployment.yaml
                    kubectl --kubeconfig=/var/jenkins_home/.kube/config apply -f service.yaml
                """
            }
        }

    }
}
