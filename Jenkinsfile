pipeline {

    agent any

    environment {

        GCR_CREDENTIALS_ID = 'project3-ji' // The ID you provided in Jenkins credentials 

        IMAGE_NAME = 'lbg'

        GCR_URL = 'gcr.io/lbg-mea-18/ji-project3'

        PROJECT_ID = 'lbg-mea-18'

        CLUSTER_NAME = 'demo-cluster-ji'

        LOCATION = 'europe-west2-c'

        CREDENTIALS_ID = 'k8sclusterprivatekey'

    }

    stages {

        stage('Build and Push to GCR') {

            steps {

                script {

                    // Authenticate with Google Cloud

                    withCredentials([file(credentialsId: GCR_CREDENTIALS_ID, variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {

                        sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'

                    }

                    // Configure Docker to use gcloud as a credential helper

                    sh 'gcloud auth configure-docker --quiet'

                    // Build the Docker image

                    sh "docker build -t ${GCR_URL}/${IMAGE_NAME}:latest ."

                    // Push the Docker image to GCR

                    sh "docker push ${GCR_URL}/${IMAGE_NAME}:latest"

                }

            }

        }

        stage('Deploy to GKE') {

            steps {

                script {

                    // Deploy to GKE using Jenkins Kubernetes Engine Plugin

                    step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'kubernetes/deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])

                }

            }

        }

    }

}
