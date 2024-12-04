pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'http://172.28.95.37:9000'
        PROJECT_KEY = 'jobportal'
        SONARQUBE_TOKEN = 'sqp_d71cf354e237a65f6011cd03657d2fa738614901'
        SOURCE_DIR = 'https://github.com/sumitchavan0110/sonarqube.git' // Updated Source Directory
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    def gitRepoUrl = 'https://github.com/sumitchavan0110/sonarqube.git'
                    checkout([$class: 'GitSCM', branches: [[name: 'main']],
                        userRemoteConfigs: [[url: gitRepoUrl]]])
                }
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis
                    sh "sonar-scanner -Dsonar.projectKey=${PROJECT_KEY} -Dsonar.sources=${SOURCE_DIR} -Dsonar.host.url=${SONARQUBE_SERVER} -Dsonar.login=${SONARQUBE_TOKEN}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                        cd C:\Users\12726\Desktop\Jobportal-Sonarqube\sonarqube
                        docker build -t sumitchavan0110/jobportalimage:v1 .
                    '''
                }
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockercred',
                        usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                        sh "docker push sumitchavan0110/jobportalimage:v1"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                        kubectl config use-context minikube
                        cd C:\Users\12726\Desktop\Jobportal-Sonarqube\sonarqube
                        kubectl apply -f deployment.yml
                        kubectl apply -f service.yml
                        kubectl get pods
                        kubectl get deployments
                    '''
                }
            }
        }

        stage('List Minikube Services') {
            steps {
                script {
                    sh "minikube service --all"
                }
            }
        }
    }
}
