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
         withCredentials([usernameColonPassword(credentialsId: '3cbc1626-96fd-423a-98f3-e923cf5875cb', variable: 'GCP_PROJECTS'), file(credentialsId: 'c7343f7d-a750-49b7-a101-2161584ee99c', variable: 'gcp_json')]) {
         sh 'gcloud auth activate-service-account --key-file=$gcp_json'
        }
      }
    }
    stage("Docker pull") {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'b78eb4a2-ba3a-48c9-8986-ece9449c86a0', variable: 'DOCKER')]) {
        sh 'docker login -u ajaydocker21 -p @Ajay$IND21'
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
