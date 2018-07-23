pipeline {
  agent none
  options {
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
  }
  stages {
    stage('Unit Tests') {
      agent {
        label 'apache'
      }
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
     post{
       success {
         archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
       }
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
        sh "wget http://bakulgupta1.mylabserver.com/rectangle/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 4 5"
      }
    }
    stage('promoting to green') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp /var/www/html/rectangle/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangle/green/"
      }
    }
  }
}
  
