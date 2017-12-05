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
        echo 'checking out development branch'
        sh 'git checkout development'
        sh 'git pull origin devlopment'
        echo 'checkingout master'
        sh 'git checkout master'
        sh 'git pull origin master'
        echo 'merging development into master branch'
        sh 'git merge development'
        echo 'Pushing to origin master'
        sh 'git push origin master'
}
}
}
}
