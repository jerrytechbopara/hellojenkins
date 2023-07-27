def dynamic_stages = [:]
for (int i = 0; i < output.size(); i++) {
    def var = output[i]
  dynamic_stages["Stage-${var.NODE_ENV}"] = {
    container('docker') {
        script {
            docker.withRegistry( "https://${REGISTRY}", registryCred ) {
                dockerImage = docker.build("${REGISTY_NAME}/${var.NODE_ENV}:front-${env.BUILD_NUMBER}", "--build-arg NODE_ENV=${var.NODE_ENV} ./client")
                dockerImage.push()
            }
        }
        script {
            docker.withRegistry( "https://${REGISTRY}", registryCred ) {
                dockerImage = docker.build("${REGISTY_NAME}/${var.NODE_ENV}:api-${env.BUILD_NUMBER}", "--build-arg NODE_ENV=${var.NODE_ENV} .")
                dockerImage.push()
            }
        }
    }
    container('app') {
        script {
            sh """
                kubectl get ns
            """
        }
    }
  }
}

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
        GCLOUD_SQL_CONNECTION_STRING_DEV = 'dark-automata-243312:'
        GCLOUD_SQL_CONNECTION_STRING_PROD = 'dark-automata-243312'
        REGISTRY = 'kmt3nw9g.gra7'
        REGISTY_NAME = 'library'
        gitCred = '1111111'
        registryCred = '111111111'
        GIT_URL = 'https://github.com/jerrytechbopara/hellojenkins.git'
        GIT_BRANCH = 'master'
        HELM_REPLICA_COUNT = '1'
    }

    stages {
        stage('Git checkout') {
          steps {
            checkout([$class: 'GitSCM', branches: [[name: "${GIT_BRANCH}"]], extensions: [], userRemoteConfigs: [[credentialsId: "${gitCred}", url: "${GIT_URL}"]]])   
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
        stage ('Dynamic Portals') {
            steps {
                script {
                    parallel dynamic_stages
                }
            }
        }
    }
}
