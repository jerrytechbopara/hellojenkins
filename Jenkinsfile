node {
  try {
    stage('checkout') {
      checkout scm
    }
    stage('prepare') {
      sh "git clean -fdx"
    }
    stage('compile') {
      sh  "cat /etc/issue"
    }
    stage('publish') {
      kubeconfig(credentialsId: 'sydk8s', serverUrl: 'https://6dezvm.c1.syd1.k8s.ovh.net') {
        sh "kubectl get ns"
    }
    }
  } finally {
    stage('cleanup') {
      echo "doing some cleanup..."
    }
  }
}
