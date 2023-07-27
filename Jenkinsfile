pipeline {
    agent {
        kubernetes {
          label 'portal'
          yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              securityContext:
                runAsUser: 0
              containers:
              - name: app
                image: alpine/k8s:1.22.15
                tty: true
                command:
                - /bin/cat
              - name: docker
                image: docker:dind
                imagePullPolicy: Always
                command:
                - dockerd
                - --host=unix:///var/run/docker.sock
                - --host=tcp://0.0.0.0:2375
                - --storage-driver=overlay2
                securityContext:
                  privileged: true
          '''
        }
    }

    options {
        disableConcurrentBuilds()
        timeout(time: 60, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '100'))
    }
  
    environment {
        Hello= 'pment'
    }

    stages {
        stage('Git checkout') {
          steps {
            sh "echo 'helo'"
          }
        }
        stage ('Install Git') {
            steps {
                container('docker') {
                    script {
                        sh 'apk add git'
                    }
                }
            }
        }
        stage ('kubectl command') {
            steps {
                container('app') {
                    script {
			kubeconfig(credentialsId: 'sydk8s', serverUrl: 'https://6dezvm.c1.syd1.k8s.ovh.net') {
                        sh """
			    kubectl get pods
			    kubectl get ns
		
			"""
			}
                    }
                }
            }
        }
    }
}
