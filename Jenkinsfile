pipeline {
    agent any
    environment {
        AZURE_CREDENTIALS = 'azure-lucky'      // Your Jenkins Azure credential ID
        ACR_NAME = 'luckyregistry'             // Your Azure Container Registry name
        IMAGE_NAME = 'springbootapp'           // Docker image name
        AKS_RESOURCE_GROUP = 'demo11'          // AKS resource group
        AKS_CLUSTER_NAME = 'lucky-aks'         // AKS cluster name
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Arshad9555/enahanced-petclinc-springboot.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $ACR_NAME.azurecr.io/$IMAGE_NAME:latest .'
            }
        }

        stage('Docker Push to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'acr-secret', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login $ACR_NAME.azurecr.io --username $DOCKER_USER --password-stdin'
                    sh 'docker push $ACR_NAME.azurecr.io/$IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                azureCLI(credentialsId: AZURE_CREDENTIALS, script: '''
                    az aks get-credentials --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME
                    kubectl apply -f springboot-k8s/springboot-deploy.yaml
                ''')
            }
        }
    }
}

