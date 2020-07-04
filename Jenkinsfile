pipeline{
  agent any
  tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "maven"
   }
stages {
  /*
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
  */

  stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "txdevops",
                    url: "http://34.71.101.126:8082/artifactory",
                    credentialsId: "jfrog"
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "txdevops",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "txdevops",
                    
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
                
            }
        }
        stage ('Update version') {
          steps {

               sh 'sed -i -e s/BUILD_NUMBER/1.0.${BUILD_NUMBER}/g $WORKSPACE/project/src/main/webapp/index.html'
               sh 'sed -i -e s/BUILD_NUMBER/1.0.${BUILD_NUMBER}/g $WORKSPACE/project/src/main/Webapp/index.html'


          }



        }
        stage ('Cleanup deploy folder'){

          steps{
              sh 'rm -rf $WORKSPACE/bazinga'
            }
        }

  stage ('Exec Maven') {


            steps {
               
                slackSend channel: '#superleague', message: 'Build  started'

                rtMavenRun (
                                tool: "maven", // Tool name from Jenkins configuration
                                pom: 'pom.xml',
                                goals: 'clean install ',
                                deployerId: "MAVEN_DEPLOYER",
                                resolverId: "MAVEN_RESOLVER"
                            )
                rtDownload (
                  serverId: 'txdevops',
                  failNoOp: true,
                  spec: '''{
                    "files": [
                    {
                      "pattern": "libs-release-local/ci/jenkins/aws/project/1.0.${BUILd_NUMBER}/*.war",
                      "target": "bazinga/"
                    }
                  ]
                  }''',

                    
                )
                    }
            post {
                always {
                    //slackSend channel: '#cicd', message: 'Build Completed '
                    slackSend channel: '#superleague', message: 'Build completed.'
                    
                    
                    }
                }
        }

  stage('Sonarqube') {
              environment {
              scannerHome = tool 'sonarqube-4.3'
              }
        steps {
              withSonarQubeEnv('sonar') {
              sh "${scannerHome}/bin/sonar-scanner -Dsonar.project.settings=${WORKSPACE}/sonar-project.properties"
              }
          }
    }
 stage ('Deployment') {
           
            steps {

                deploy adapters: [tomcat8(credentialsId: 'tomcat8', path: '', url: 'http://35.239.253.10:8080')], contextPath: 'WebApp', war: '**/bazinga/**/*.war'
            }
             post {
                always {
                    //slackSend channel: '#cicd', message: 'Build Completed '
                    slackSend channel: '#superleague', message: 'Build  deployed.'
                    
                    
                    }
                }

}

}
}
