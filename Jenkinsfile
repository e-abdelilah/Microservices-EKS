pipeline {
    agent any

    stages {
        stage('Deploy to kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS', contextName: '', credentialsId: 'k8s-token', namespace: 'webapps', serverUrl: 'https://7B4E4FC235E62A0AB56C1687F9337ED3.gr7.eu-north-1.eks.amazonaws.com']]) {
                     sh "kubectl apply -f deployment-service.yml"
                     
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                 withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS', contextName: '', credentialsId: 'k8s-token', namespace: 'webapps', serverUrl: 'https://7B4E4FC235E62A0AB56C1687F9337ED3.gr7.eu-north-1.eks.amazonaws.com']]) {
                     sh "kubectl get all -n webapps"
                }
            }
        }
    }
}
