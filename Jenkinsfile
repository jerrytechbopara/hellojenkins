node {
  try {
    stage('checkout') {
      checkout scm
    }
    stage('prepare') {
      sh "git clean -fdx"
    }
    stage('compile') {
      sh  "echo 'Hello'"
    }
    stage('publish') {
        sh "whereis kubectl'"
    }
  } finally {
    stage('cleanup') {
      echo "doing some cleanup..."
    }
  }
}
