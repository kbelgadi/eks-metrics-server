pipeline {
    agent {
        docker { image '897964440075.dkr.ecr.eu-west-1.amazonaws.com/ecr_demo_dev:kubectl-helm-0.1' }
    }
    environment {
        EKS_NAME = "eks_demo_dev"
        AWS_REGION = "eu-west-1"
        KUBECONFIG="/tmp/config"
    } 
    stages {
        stage('context') {
            steps {
                sh '''
                  aws eks --region ${AWS_REGION} update-kubeconfig --name ${EKS_NAME}
                '''
            }
        }
        stage ("deploy metrics server"){
               steps { 
                sh '''
                  kubectl apply -f *.yaml
                '''
               }
        }
        stage ("check"){
               steps { 
                sh '''
                  count=7; 
                  while [ $(kubectl get pods -l k8s-app=metrics-server -o 'jsonpath={..status.conditions[?(@.type=="Ready")].status}') != "True" -o $count -eq 0 ]; do 
                    echo "waiting for pod"
                    sleep 1
                    count=$((count - 1))
                  done
                '''
               }
        }
    }
}