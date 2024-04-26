pipeline {
  agent {
    docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
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
    // stage('Build and Test') {
    //   steps {
    //     sh 'ls -ltr'
    //     // build the project and create a JAR file
    //     sh 'cd  mvn clean package'
    //   }
    // }
    // stage('Static Code Analysis') {
    //   environment {
    //     SONAR_URL = "http://localhost:9000"
    //     SONAR_AUTH_TOKEN = "39e43564678269e2a20c9fc159dfb7243ea1a559"
    //   }
    //   steps {
    //     withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
    //       sh 'cd E_shop && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
    //     }
    //   }
    // }

    stage('Install Dependencies') {
            steps {
                sh "npm install"
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
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "docker build --build-arg TMDB_V3_API_KEY=<yourtmdbapikey> -t netflix ."
                       sh "docker tag netflix nasi101/netflix:latest "
                       sh "docker push nasi101/netflix:latest "
                    }
                }
            }
        }
    stage("TRIVY"){
            steps{
                sh "trivy image nasi101/netflix:latest > trivyimage.txt" 
            }
        }
    stage('Deploy to container'){
            steps{
                sh 'docker run -d -p 8081:80 nasi101/netflix:latest'
            }
        }
    
   }

   


}