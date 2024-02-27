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
    }
}
