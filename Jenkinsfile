pipeline {
  agent any
  stages {
    stage("Clear Workspace"){
        steps {
          sh 'rm -rvf t*'
        }
    }
    stage("Checkout") {
        steps {
        sh 'git clone https://github.com/KrishnaMaddila/tomcat.git'
      }
    }
    stage('GCP Auth') {
        steps {
         withCredentials([usernameColonPassword(credentialsId: 'GCP_PROJECT', variable: 'GCP_PROJECT'), file(credentialsId: 'GCP_CREDENTIALS', variable: 'GCP_CREDENTIALS')]) {
         sh 'gcloud auth activate-service-account --key-file=$GCP_CREDENTIALS'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'cc4576a5-5d72-495e-aef7-74b11ce9e03e', variable: 'Docker_credentials')]) {
        sh "docker login -u krishnavirat -p Krishna@2000"
        sh 'docker pull krishnavirat/apachetomcat:tag2'
       }
     }
    }
    stage("Docker tag") {
      steps {     
         sh 'docker tag krishnavirat/apachetomcat:tag2 gcr.io/model-argon-389809/tomcat:test1'
      }
    }
    stage("Docker push") { 
        steps {
             sh 'gcloud auth configure-docker'
             sh 'docker push gcr.io/model-argon-389809/tomcat:test1'
           }
        }
     stage("cluster create") {
       steps {
          sh 'gcloud container clusters create tomcat-cluster --num-nodes 3 --location=asia-south1-b'
       }
     }
    stage("Create & expose deploy") {
       steps {
         sh 'kubectl create deployment apachetomcat --image=gcr.io/model-argon-389809/tomcat:test1'
         sh 'kubectl expose deployment apachetomcat --type=LoadBalancer --port 80 --target-port 8080'
       }
    }
  }  
}  
