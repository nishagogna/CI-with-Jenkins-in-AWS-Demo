pipeline{
  agent any
stages {
  stage ('Build') {
    steps {
     sh 'mvn package'
    }
    post {
      always {
      archiveArtifacts artifacts: 'project/target/*.war', followSymlinks: false onlyIfSuccessful: true
      }
    }
  }

}

}
