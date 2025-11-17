pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "alikallel/mon-app:latest"
        HELM_CHART_PATH = "mon-app"
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

                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker build -t alikallel/mon-app:latest .
                        '''
                    }
                }
            }
        }

        stage('Pousser image Docker') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', 
                        usernameVariable: 'DOCKER_USERNAME', 
                        passwordVariable: 'DOCKER_PASSWORD')]) {

                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push alikallel/mon-app:latest
                        '''
                    }
                }
            }
        }

       stage('Déployer avec Helm') {
            steps {
                sh """
                    helm upgrade --install mon-app ./mon-app --kubeconfig /var/jenkins_home/.kube/config
                """
            }
        }

    }
}
