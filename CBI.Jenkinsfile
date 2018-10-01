pipeline {
  agent any

  options {
    buildDiscarder(logRotator(numToKeepStr:'15'))
  }

  tools { 
    maven 'apache-maven-latest'
    jdk 'openjdk-jdk-latest'
  }
  
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Maven Build') {
      steps {
        dir('build') { deleteDir() }
        dir('.m2/repository/org/eclipse/xtext') { deleteDir() }
        dir('.m2/repository/org/eclipse/xtend') { deleteDir() }
        sh '''
          mvn \
            -f org.eclipse.xtext.maven.parent/pom.xml \
            --batch-mode \
            --update-snapshots \
            -fae \
            -PuseJenkinsSnapshots \
            -DJENKINS_URL=$JENKINS_URL \
            -Dmaven.test.failure.ignore=true \
            -Dmaven.repo.local=${workspace}/.m2/repository \
            -DJENKINS_URL=$JENKINS_URL \
            -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn \
            clean deploy
        '''
      }
    }
  }

  post {
    success {
      archiveArtifacts artifacts: 'build/**'
    }
  }
}