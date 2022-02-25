pipeline {
    agent any
    environment {
        PROJECT_ID = 'niveus-technology-rnd'
        CLUSTER_NAME = 'faz'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'kubernetes'
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
                    myapp = docker.build("fazilniveus/abc:${env.BUILD_ID}")
                }
            }
        }

        stage("Push Docker Image") {
            steps {
	            script {
				    echo "Push Docker Image"
				    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
            				sh "docker login -u fazilniveus -p ${dockerhub}"
				    }
				        myapp.push("${env.BUILD_ID}")
				    
			    }
		    }
	    }

        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }

    }    
}
