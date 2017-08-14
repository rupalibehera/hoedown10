#!/usr/bin/groovy
@Library('github.com/rupalibehera/fabric8-pipeline-library@release-version-211')

def failIfNoTests = ""
try {
  failIfNoTests = ITEST_FAIL_IF_NO_TEST
} catch (Throwable e) {
  failIfNoTests = "false"
}

def localItestPattern = ""
try {
  localItestPattern = ITEST_PATTERN
} catch (Throwable e) {
  localItestPattern = "*KT"
}


def versionPrefix = ""
try {
  versionPrefix = VERSION_PREFIX
} catch (Throwable e) {
  versionPrefix = "1.0"
}
def flow = new io.fabric8.Fabric8Commands()

def utils = new io.fabric8.Utils()
def label = "buildpod.${env.JOB_NAME}.${env.BUILD_NUMBER}".replace('-', '_').replace('/', '_')

mavenNode{
  checkout scm
  if (utils.isCI()){
    
    mavenCI{}
    
  } else if (utils.isCD()){
  
    echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
    container(name: 'maven') {

      stage('Build Release'){
        def projectVersion = flow.getProjectVersion()
        def canaryVersion = flow.getNextReleaseVersion(projectVersion)
        mavenCanaryRelease {
          version = canaryVersion
        }
      }
      
      stage('Integration Test'){
        mavenIntegrationTest {
          environment = 'Test'
          failIfNoTests = localFailIfNoTests
          itestPattern = localItestPattern
        }
      }
    }
  }
}
