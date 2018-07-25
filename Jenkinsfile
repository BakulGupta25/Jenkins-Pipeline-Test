pipeline {
  agent none
  }
  environment {
    MAJOR_VERSION = 1
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
        sh "mkdir -p /var/www/html/rectangle/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangle/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
    stage('running jar') {
      agent {
        label 'apache'
      }
      steps {
	sh "cd ~"
        sh "wget http://bakulgupta1.mylabserver.com/rectangle/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 4 5"
      }
    }
    stage('promoting to green') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        sh "cp /var/www/html/rectangle/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangle/green/${env.BRANCH_NAME}"
      }
    }
    stage('Promote development branch to Master') {
      agent {
        label 'apache'
      }
      when {
        branch 'Development'
      }
      steps {
      echo "Stashing any local changes"
      sh 'git stash'
      echo "Checking out developement branch"
      sh 'git checkout Development'
      echo "Checking out Master Branch"
      sh 'git checkout Master'
      echo "Merging Development into Master Branch"
      sh 'git merge Development'
      echo 'Push to Origin Master'
      sh 'git push origin master'
      echo 'Tagging the release'
      sh "git tag Rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      sh "git push origin Rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
    }
  }
}
