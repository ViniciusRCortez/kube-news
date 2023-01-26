pipeline{
    agent any

    stages {

        stage ("Build Docker Image"){
            steps{
                script{
                    dockerapp = docker.build("vinisicius/kube-news:${env.BUILD_ID}", "-f ./src/Dockerfile ./src")
                }
            }
        }

        stage ("Push Docker Image"){
            steps{
                script{
                    docker.withRegistry("https://registry.hub.docker.com", "docker_hub"){
                        dockerapp.push("${env.BUILD_ID}")
                        dockerapp.push("latest")

                    }
                }
            }
        }

        stage ("Deploy Kubernets"){
            steps{
                withKubeConfig([credentialsId: "kubeconfig"]){
                    sh "kubectl apply -f ./k8s/deployment.yml"
                }
            }
        }

    }
}