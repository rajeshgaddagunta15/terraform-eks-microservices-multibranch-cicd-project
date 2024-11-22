pipeline {
    agent any

    stages {
        stage('Deploy to Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'demo_cluster_eks', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://38C1EFF4B3CDDD74AB2C211080031265.gr7.ap-south-1.eks.amazonaws.com']]) {
                   
                   sh "kubectl apply -f deployment-service.yml"
                   sleep 60
                 
                 
                  }
            }
        }
        
        
        stage('Verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'demo_cluster_eks', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://38C1EFF4B3CDDD74AB2C211080031265.gr7.ap-south-1.eks.amazonaws.com']]) {
                   
                   sh "kubectl get all -n webapps"
                   
                 
                 
                  }
            }
        }
    }
}
