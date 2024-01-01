pipeline {
  agent any
  stages {
    stage("Clear Workspace"){
        steps {
          sh 'rm -rvf G*'
        }
    }
    stage("Checkout") {
        steps {
        sh 'git clone https://github.com/Ajayk21/Grafana-promethus.git'
      }
    }
    stage('GCP Auth') {
        steps {
         withCredentials([usernameColonPassword(credentialsId: 'c29c6656-7660-4108-a621-457c8b4548f2', variable: 'GCP_PROJECTS'), file(credentialsId: 'cdc4f58c-35e6-4888-9bf4-75d7ae8deeca', variable: 'gcp_json')]) {
         sh 'gcloud auth activate-service-account --key-file=$gcp_json'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'a1bc1715-8bf9-41c7-becf-3e2fe8e3ed67', variable: 'docker')]) {
        sh 'docker login -u ajaydocker21 -p @Ajay83449'
        sh 'docker pull ajaydocker21/project3-grafana:tag1'
       }
     }
    }
    stage("Docker tag") {
      steps {     
         sh 'docker tag ajaydocker21/project3-grafana:tag1 gcr.io/model-argon-389809/grafana:test1'
      }
    }
    stage("Docker push") { 
        steps {
             sh 'gcloud auth configure-docker'
             sh 'docker push gcr.io/model-argon-389809/grafana:test1'
           }
        }
     stage("cluster create") {
       steps {
          sh 'gcloud container clusters create grafana-cluster --num-nodes 3 --location=europe-central1-c'
       }
     }
    stage("Create & expose deploy") {
       steps {
         sh 'kubectl create deployment grafana --image=gcr.io/model-argon-389809/grafana:test1'
         sh 'kubectl expose deployment grafana --type=LoadBalancer --port 80 --target-port 3000'
       }
    }
  }  
}  
