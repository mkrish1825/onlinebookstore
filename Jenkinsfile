pipeline {  
    agent any  
        stages {  
       	    stage("git_checkout") {  
           	    steps {  
              	  git 'url'
              	    }  
         }
    tools {
        maven 'maven3' 
    }
      stage ('Build') {
        steps {
          script{
            def mvnHome = tool name: 'maven3', type: 'maven'
            sh "mvn package"
            //sh 'mv target/onlinebookstore*.war target/newbook.war'
          }
        }
      }
      stage ('SonarQube'){
        steps{
          script{
            def mvnHome =  tool name: 'maven3', type: 'maven'
            withSonarQubeEnv('sonar-pro') {
              sh "${mvnHome}/bin/mvn sonar:sonar"
            }
          }
        }
      }
      stage('Docker Build') {
        steps{
          script{
            sh 'docker build -t santhoshkumarsk/multi:v2 .'
            //sh 'docker images'
          }
        }
      }
      stage('Docker push'){
        steps{
          script{
            withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerPassword')]) {
              sh "docker login -u santhoshkumarsk -p ${dockerPassword}"
              sh 'docker push santhoshkumarsk/multi:v2'
              sh 'docker rmi santhoshkumarsk/multi:v2'
            }
          }
        }
      }
      stage('Deploy on k8s') {    
        steps {
          script{
            withKubeCredentials(kubectlCredentials: [[ credentialsId: 'k8', namespace: 'ms' ]]) {
                sh 'kubectl apply -f kube.yaml'
            }
          }
        }
      }
    }
}
        
