pipeline {
    agent any
    environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short HEAD")
    }
    stages {
        stage('Connect gke') {
            steps {
                sh 'gcloud container clusters get-credentials gke-east1 --zone us-east1-b --project cloudside-academy'
            }
        }
        stage('Build Image') {
            steps {
                git branch: 'main', url: 'https://github.com/tsa-cloudside/jenkins.git'
                sh 'sudo docker build -t gcr.io/cloudside-academy/nginx-svc:$tag .'
            }
        }
        stage('Push Image') {
            steps {
               sh 'sudo gcloud auth configure-docker gcr.io -q'
               sh 'sudo docker images'
               sh 'sudo docker push gcr.io/cloudside-academy/nginx-svc:$tag'
            }
        }
        stage('Image remove'){
            steps{
                sh 'sudo docker rmi gcr.io/cloudside-academy/nginx-svc:$tag'
            }
        }
        stage("Set up Kustomize"){
            steps{
                sh 'curl -sfLo kustomize https://github.com/kubernetes-sigs/kustomize/releases/download/v3.1.0/kustomize_3.1.0_linux_amd64'
                sh 'chmod u+x ./kustomize'
            }
        }
        stage("Deploy Image to GKE cluster"){
            steps{
                sh 'kubectl get nodes'
                sh './kustomize edit set image gcr.io/PROJECT_ID/IMAGE:TAG=gcr.io/cloudside-academy/nginx-svc:$tag'
                sh './kustomize build . | kubectl apply -f -'
                sleep 60
                sh 'kubectl get services -o wide'
            }
        }    pipeline {
    agent any
    environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short HEAD")
    }
    stages {
        stage('Connect gke') {
            steps {
                sh 'gcloud container clusters get-credentials gke-east1 --zone us-east1-b --project cloudside-academy'
            }
        }
        stage('Build Image') {
            steps {
                git branch: 'main', url: 'https://github.com/tsa-cloudside/jenkins.git'
                sh 'sudo docker build -t gcr.io/cloudside-academy/nginx-svc:$tag .'
            }
        }
        stage('Push Image') {
            steps {
               sh 'sudo gcloud auth configure-docker gcr.io -q'
               sh 'sudo docker images'
               sh 'sudo docker push gcr.io/cloudside-academy/nginx-svc:$tag'
            }
        }
        stage('Image remove'){
            steps{
                sh 'sudo docker rmi gcr.io/cloudside-academy/nginx-svc:$tag'
            }
        }
        stage("Set up Kustomize"){
            steps{
                sh 'curl -sfLo kustomize https://github.com/kubernetes-sigs/kustomize/releases/download/v3.1.0/kustomize_3.1.0_linux_amd64'
                sh 'chmod u+x ./kustomize'
            }
        }
        stage("Deploy Image to GKE cluster"){
            steps{
                sh 'kubectl get nodes'
                sh './kustomize edit set image gcr.io/PROJECT_ID/IMAGE:TAG=gcr.io/cloudside-academy/nginx-svc:$tag'
                sh './kustomize build . | kubectl apply -f -'
                sleep 60
                sh 'kubectl get services -o wide'
            }
        }    
