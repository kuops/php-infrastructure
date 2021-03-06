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
    image: dtzar/helm-kubectl:3.1.1
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


    parameters {
        string defaultValue: '10.7.0.102', description: 'INGRESS_NODE_IP', name: 'INGRESS_NODE_IP', trim: false
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

        stage('deploy blue version') {
            
            steps {
                container('helm') {
                    sh """
                      sed -i "s@{.INGRESS_NODE_IP}@${INGRESS_NODE_IP}@g" voyager/values.yaml
                      helm template blue voyager|kubectl apply -n default -f -
                      kubectl rollout -n default status deployment blue-voyager
                    """
                }

            }
        }

        stage('deploy green version') {
            
            steps {
                container('helm') {
                    sh """
                      sed -i 's@master@canary@g' voyager/values.yaml
                      helm template green voyager|kubectl apply -n default -f -
                      kubectl rollout -n default status deployment green-voyager
                    """
                }

            }
        }

    }
}
