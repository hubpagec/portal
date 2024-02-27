pipeline {
    agent any

    stages('Construção da imagem'){
        stage('Checkout Source') {
            steps{
                git url: 'https://github.com/hubpagec/portal', branch: 'master'
            }
        }
        stage('Build Docker image') {
            steps{
                script{
                    dockerapp = docker.build("hubpagec/portal:${env.BUILD}",
                        '-f ./Dockerfile .')
                }

            }
        }
        stage('Push Docker Image') {
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy Kubernetes') {
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                script {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
                    sh 'cat ./k8s/deployment.yaml'
                    kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig')
                }
            }
        }

    }
}
