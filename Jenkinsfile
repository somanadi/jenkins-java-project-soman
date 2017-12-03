pipeline {
  agent none

  
  stages {
    stage('Unit Tests'){
      steps {
        agent { 
          label 'apache'
        }
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
       sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all"
       }
     }
 
   stage("Running on CentOS") {
      agent {
          label 'apache'
       }
     steps{
       sh "wget http://iamsoman1.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
       sh "java -jar rectangle_${env.BUILD_NUMBER} 3 4"
	}
     }
 }
  post {
    always {
	archiveArtifacts  artifacts: 'dist/*.jar', fingerprint:true
	}
    }

}
