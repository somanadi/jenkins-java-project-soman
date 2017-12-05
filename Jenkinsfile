pipeline {
  agent none

  
  stages {
    stage('Unit Tests'){
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
     post {
       always {
          archiveArtifacts  artifacts: 'dist/*.jar', fingerprint:true
        }
    }
     }
   
   stage('deploy') {
      agent {
          label 'apache'
       }
     steps {
       sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
       sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
       }
     }
 
   stage("Running on CentOS") {
      agent {
          label 'apache'
       }
     steps{
       sh "wget http://iamsoman1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
       sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
	}
     }


  stage("Test on Debian"){
	agent {
	  docker 'openjdk:8u121-jre'
         }
        steps{
           sh "wget http://iamsoman1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"  
           sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
        }
    }

 stage("Promote to green") {
    agent{
       label 'apache'
       }
     when {
      branch 'master'
      }
     steps{
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar  /var/www/html/rectangles/green/"
	}
      }
    
   stage("Promote Development Branch to Master") {
     agent {
       label 'apache'

    }
     when {
       branch 'development'
      }
     steps {
        echo 'Stashing any Local Changes'
        sh ' git stash'
        echo 'checking out development branch'
        sh 'git checkout development'
        echo 'checking out master'
        sh 'git checkout master'
        echo 'Merging development into master branch'
        sh 'git merge development'
        echo 'Pusing to origin Master'
        sh 'git push origin master'
}
}
}
}
