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
            
            environment{
                tag_version = "${env.BUILD_ID}"
            }

            steps{
                withKubeConfig([credentialsId: "kubeconfig"]){
                    sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yml'
                    sh "kubectl apply -f ./k8s/deployment.yml"
                }
            }
        }

    }
}