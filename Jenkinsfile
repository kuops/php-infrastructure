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
  containers:
  - name: helm
    image: lachlanevenson/k8s-helm:v2.12.1
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /root/.kube
      name: kube-conf    
  - name: kubectl
    image: lachlanevenson/k8s-kubectl:v1.13.2
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /root/.kube
      name: kube-conf
  volumes:
  - name: kube-conf
    hostPath:
      path: /root/.kube/
      type: Directory
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
                    /usr/local/bin/helm init --upgrade --service-account tiller \
	                --skip-refresh -i kuops/tiller:v2.12.1 
	                /usr/local/bin/helm ls
                    """
                }
            }
        }

        stage('deploy dev') {
            
            steps {
                container('helm') {
                    CONFIGMAP_MD5 = sh (
                        script: 'md5sum voyager/templates/configmap.yaml|awk \'{print $1}\'',
                        returnStdout: true
                    ).trim()
                    sh """
                    pwd
                    sed -i  "/name: CONFIGMAP_MD5/{n;s@\".*\"@\"${CONFIGMAP_MD5}\"@g}" voyager/templates/deployment.yaml
                    helm  upgrade mysql-dev mysql --install --namespace=dev --wait
                    helm  upgrade php-app voyager --install --namespace=dev --wait
                    """
                }
            }
        }

    }
}
