pipeline {
  agent {
    docker {
      image 'sushantjadhavhcl/docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
  stages 
  {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        git branch: 'master', url: 'https://github.com/sushantjadhav416/Netflix-Project-Pipline'
      }
    }
    stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker'){   
                       sh "docker build --build-arg TMDB_V3_API_KEY=<yourtmdbapikey> -t netflix ."
                       sh "docker tag sushantjadhavhcl/netflix:latest "
                       sh "docker push sushantjadhav/netflix:latest "
                    }
                }
            }
        }
    stage("TRIVY"){
            steps{
                sh "trivy image sushantjadhavhcl/netflix:latest > trivyimage.txt" 
            }
        }
    stage('Deploy to container'){
            steps{
                sh "docker run -d -p 8081:80 sushantjadhavhcl/netflix:latest"
            }
    }
   }
   


}