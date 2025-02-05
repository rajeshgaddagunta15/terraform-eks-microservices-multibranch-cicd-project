pipeline {
    agent any

    stages {
        stage('Deploy to Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'demo_cluster_eks', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://0A1D96635762CE8A4B96AAEDAED8DFDC.gr7.us-east-1.eks.amazonaws.com']]) {
                 
                   sh "kubectl apply -f deployment-service.yml"
            
                 
                 
                  }
            }
        }
        
        
        stage('Verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'demo_cluster_eks', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://0A1D96635762CE8A4B96AAEDAED8DFDC.gr7.us-east-1.eks.amazonaws.com']]) {
                   
                   sh "kubectl get svc -n webapps"
                   
                 
                 
                  }
            }
        }
    }
}
