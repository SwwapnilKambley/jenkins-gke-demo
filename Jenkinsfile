pipeline {
    agent any
    environment {
        PROJECT_ID = "dragonikspidey"
        CLUSTERNAME = "blue-cluster"
        LOCATION = "asia-southeast1-a"
        CREDENTIALS = "dragonikspidey"
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("skambley/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push Image") {
            steps {
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerID') {
                        myapp.push("latest")
                        myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage("Deploy to GKE") {
            steps {
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }
}