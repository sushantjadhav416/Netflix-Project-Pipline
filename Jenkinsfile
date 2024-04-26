pipeline{
    agent {
    docker {
      image 'sushantjadhavhcl/docker-agent:I20240425'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
  }
    stages {
        // stage('clean workspace'){
        //     steps{
        //         cleanWs()
        //     }
        // }
        stage('Checkout from Git'){
            steps{
                git branch: 'master', url: 'https://github.com/sushantjadhav416/Netflix-Project-Pipline.git'
            }
        }
        // stage("Sonarqube Analysis "){
        //     steps{
        //         withSonarQubeEnv('sonar-server') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
        //             -Dsonar.projectKey=Netflix '''
        //         }
        //     }
        // }
        // stage("quality gate"){
        //    steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
        //         }
        //     } 
        // }
        stage('Install Dependencies') {
            steps {
                sh "apt-get install npm && yes" 
               
            }
        }
        // stage("TRIVY"){
        //     steps{
        //         sh "sudo apt-get install wget apt-transport-https gnupg lsb-release" 
        //         sh "wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -" 
        //         sh "echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list" 
        //         sh "sudo apt-get update"
        //         sh "sudo apt-get install trivy" 
        //     }
        // }
        // stage('OWASP FS SCAN') {
        //     steps {
        //         dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push"){
            steps{
                script
                {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker')
                   {
                       sh "docker build --build-arg TMDB_V3_API_KEY=c9f40f4b6f41c59e1459fd2b1de3f7aa netflix ."
                       sh "docker tag netflix sushantjadhavhcl/netflix:latest "
                       sh "docker push sushantjadhavhcl/netflix:latest "
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
                sh 'docker run -d -p 8091:80 sushantjadhavhcl/netflix:latest'
            }
        }
        // stage('Deploy to kubernets'){
        //     steps{
        //         script{
        //             dir('Kubernetes') {
        //                 withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
        //                         sh 'kubectl apply -f deployment.yml'
        //                         sh 'kubectl apply -f service.yml'
        //                 }   
        //             }
        //         }
        //     }
        // }

    }
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'sushantjadhav416@gmail.com',                                
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
}