node {
  try {
    stage('checkout') {
      checkout scm
    }
    stage('prepare') {
      sh "git clean -fdx"
    }
    stage('compile') {
      echo "nothing to compile for hello.sh..."
    }
    stage('publish') {
      kubeconfig(credentialsId: 'sydk8s', serverUrl: 'https://6dezvm.c1.syd1.k8s.ovh.net') {
        kubectl get ns
    }
    }
  } finally {
    stage('cleanup') {
      echo "doing some cleanup..."
    }
  }
}
