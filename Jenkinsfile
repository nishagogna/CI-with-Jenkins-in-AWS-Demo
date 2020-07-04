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

  stage ('Exec Maven') {
            steps {
               // slackSend channel: '#cicd', message: 'Build Started'

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
            /*post {
                always {
                    //slackSend channel: '#cicd', message: 'Build Completed '
                    //jiraSendBuildInfo branch: 'DEMO-1', site: 'txdevopsbootcamp.atlassian.net'
                    //jiraComment body: "Build 'env.BUILD_NUMBER' completed with commit ", issueKey: 'DEMO-1'
                    
                    
                    }
                }*/
        }
 stage ('Deployment') {
           
            steps {
                sh "ls -ltr ${WORKSPACE}/bazinga/ci/jenkins/aws/project/1.0.${BUILD_NUMBER}/*.war"
                sh "id"
                sh "sudo scp   ${WORKSPACE}/bazinga/ci/jenkins/aws/project/1.0.${BUILD_NUMBER}/*.war 107.178.222.65:"
                //sh "ssh -i /var/lib/jenkins/keys/caseStudy.pem  ubuntu@13.58.247.254 sudo mv JavaWebApp-1.0.0.101.war ProdWebapp.war"
                //sh "ssh -i /var/lib/jenkins/keys/caseStudy.pem  ubuntu@13.58.247.254 sudo cp *.war /opt/tomcat/webapps/"
                //sh "ssh -i /var/lib/jenkins/keys/caseStudy.pem  ubuntu@18.223.162.120 sudo chown tomcat:tomcat /opt/tomcat/webapps/*.war"

            }

}

}
}
