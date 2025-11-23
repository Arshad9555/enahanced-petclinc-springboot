pipeline {
    agent any

    tools {
        maven 'Maven-3.9.4'   // Must match the Maven tool name in Jenkins
        jdk 'Java-17'          // Must match your JDK tool name in Jenkins
    }

    environment {
        IMAGE_NAME       = 'springbootapp'
        IMAGE_TAG        = 'latest'
        TENANT_ID        = '7d27d7a1-c087-4346-b17d-694070fd86e2'
        ACR_NAME         = 'luckyregistry'
        ACR_LOGIN_SERVER = 'luckyregistry.azurecr.io'
        FULL_IMAGE_NAME  = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}"
        RG               = "bootproject1"
        NAME             = "lucky-aks"
    }

    stages {
        stage('Checkout FROM GIT') {
            steps {
                git branch: 'prod', url: 'https://github.com/Arshad9555/enahanced-petclinc-springboot.git'
            }
        }

        stage('Maven Package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    echo "Building Docker Image..."
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Azure Login TO ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-spn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                    sh '''
                        az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                        az acr login --name $ACR_NAME
                    '''
                }
            }
        }

        stage('Docker Push to ACR') {
            steps {
                sh '''
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                    docker push ${FULL_IMAGE_NAME}
                '''
            }
        }

        stage('Azure Login TO AKS') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-spn', usernameVariable: 'AZURE_USERNAME', passwordVariable: 'AZURE_PASSWORD')]) {
                    sh '''
                        az login --service-principal -u $AZURE_USERNAME -p $AZURE_PASSWORD --tenant $TENANT_ID
                        az aks get-credentials --resource-group $RG --name $NAME --overwrite-existing
                    '''
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                sh 'kubectl apply -f k8s/sprinboot-deployment.yaml'
            }
        }
    }
}
