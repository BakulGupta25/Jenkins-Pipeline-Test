pipeline {
  agent none

  options {
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
  }
  stages {
    stage('Unit Tests') {
      steps {
        sh 'ant -f test.xml -v'
	junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'apache'
      }
     steps {
        sh 'ant -f build.xml -v'
      }
    } 
    stage('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangle/all"
      }
    }
    stage('running jar') {
      agent {
        label 'apache'
      }
      steps {
	sh "cd ~"
        sh "wget http://13.229.90.200/rectangle/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 4 5"
      }
    }
  }
  post{
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
  
