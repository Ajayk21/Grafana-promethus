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
         withCredentials([usernameColonPassword(credentialsId: 'bee228a9-e7f6-406b-94c9-918d4bda41be', variable: 'gcp_console'), file(credentialsId: 'Json1', variable: 'gcp_json')])  {
         sh 'gcloud auth activate-service-account --key-file=$gcp_json'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: '1e9f9321-360d-4161-b669-e41bac15a08a', variable: 'DOCKER_CREDENTIALS')])  {
        sh "docker login -u ajaydocker21 -p @Ajay$IND21"
        sh 'docker pull ajaydocker21/project3-grafana:tag1'
       }
     }
    }
    stage("Docker tag") {
      steps {     
         sh 'docker tag ajaydocker21/project3-grafana:tag1 gcr.io/ajay-409305/grafana:test1'
      }
    }
    stage("Docker push") { 
        steps {
             sh 'gcloud auth configure-docker'
             sh 'docker push gcr.io/ajay-409305/grafana:test1'
           }
        }
     stage("cluster create") {
       steps {
          sh 'gcloud container clusters create grafana-cluster --num-nodes 3 --location=asia-south1-b'
       }
     }
    stage("Create & expose deploy") {
       steps {
         sh 'kubectl create deployment grafana --image=gcr.io/ajay-409305/grafana:test1'
         sh 'kubectl expose deployment grafana --type=LoadBalancer --port 80 --target-port 3000'
       }
    }
  }  
}  
