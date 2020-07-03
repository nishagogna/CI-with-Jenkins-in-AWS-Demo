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
                    url: "http://34.71.101.126:8082",
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
                    }
            /*post {
                always {
                    //slackSend channel: '#cicd', message: 'Build Completed '
                    //jiraSendBuildInfo branch: 'DEMO-1', site: 'txdevopsbootcamp.atlassian.net'
                    //jiraComment body: "Build 'env.BUILD_NUMBER' completed with commit ", issueKey: 'DEMO-1'
                    
                    
                    }
                }*/
        }

}

}
