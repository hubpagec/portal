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
                    dockerapp = docker.build("devops/portal:${env.BUILD}",
                        '-f ./Dockerfile .')
                }

            }
        }
        stage('Push Docker Image') {
            steps{
                script{
                    docker.withRegistry('http://192.168.3.138', 'harbor'){
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