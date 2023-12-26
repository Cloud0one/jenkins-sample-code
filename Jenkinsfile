pipeline {
    agent any
    environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short HEAD")
    }

        
    stages {
        
       stage("Generating OIDC token and saving it to a file") {
            steps {
                script {
                        sh 'mkdir -p /usr/share/token/'
 '                      sh (script: 'gcloud auth print-identity-token anup-repo@searce-playground-v1.iam.gserviceaccount.com  --audiences="//iam.googleapis.com/projects/512447805963/locations/global/workloadIdentityPools/test-jenkins/providers/jenkins"  > /usr/share/token/key',returnStdout: true)
                }
            }
        }
        stage ("getting the WIF config file into a variable")
        {
            steps {
                     withCredentials([file(credentialsId: 'wif-config-file', variable: 'WIF')]) 
                        {
                         sh '''
                            gcloud auth login --brief --cred-file=$WIF --quiet
                            gcloud container clusters list
                            gcloud compute instances list
                            '''

                        }
                }
        }
        stage('Connect gke') {
            steps {
                sh 'gcloud container clusters get-credentials sarvesh --zone us-central1-c --project burner-sarnaik'
            }
        }
        stage('Build Image') {
            steps {
                git branch: 'main', url: 'https://github.com/Cloud0one/jenkins-sample-code.git'
                sh 'sudo docker build -t us-central1-docker.pkg.dev/burner-sarnaik/sarvesh-jenkins:$tag .'
            }
        }
        stage('Push Image') {
            steps {
               sh 'sudo gcloud auth configure-docker gcr.io -q'
               sh 'sudo docker images'
               sh 'sudo docker push us-central1-docker.pkg.dev/burner-sarnaik/sarvesh-jenkins:$tag'
            }
        }
        stage('Image remove'){
            steps{
                sh 'sudo docker rmi us-central1-docker.pkg.dev/burner-sarnaik/sarvesh-jenkins:$tag'
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
                sh './kustomize edit set image us-central1-docker.pkg.dev/PROJECT_ID/IMAGE:TAG=us-central1-docker.pkg.dev/burner-sarnaik/sarvesh-jenkins:$tag'
                sh './kustomize build . | kubectl apply -f -'
                sleep 60
                sh 'kubectl get services -o wide'
            }
        }   
    }
}
