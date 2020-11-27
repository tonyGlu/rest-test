pipeline {
    agent any
	//ini adalah credential untuk ke google
    environment {
        PROJECT_ID = 'translate-cizen-1549780978207'
        CLUSTER_NAME = 'my-first-cluster-1'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'gke'
    }
	//sampe sini credential untuk ke gugel kubernet	
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
		stage("Build image") {
            steps {
                script {
                    myapp = docker.build("tonyglu/rest-test:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
						}
                    }
                }
			}    
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/tonyglu/rest-test:latest/rest-test:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    } 
	
}
