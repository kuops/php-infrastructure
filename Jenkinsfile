pipeline {
    agent {
        kubernetes {
        label 'mypod'
        defaultContainer 'jnlp'
        yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: helm-deploy
spec:
  serviceAccountName: example-jenkins
  containers:
  - name: helm
    image: lachlanevenson/k8s-helm:v3.1.1
    command:
    - cat
    tty: true
  - name: kubectl
    image: lachlanevenson/k8s-kubectl:v1.17.3
    command:
    - cat
    tty: true
"""
        }
    }
    
    stages {
        
        stage('init helm && kubectl') {
            steps {
                container('kubectl') {
                    sh """
                    /usr/local/bin/kubectl get pod
                    """
                }
                container('helm') {
                    sh """
	                /usr/local/bin/helm ls
                    """
                }
            }
        }

        stage('deploy dev') {
            
            steps {
                container('kubectl') {
                    sh """
                    echo "test"
                    """
                }

            }
        }

    }
}
