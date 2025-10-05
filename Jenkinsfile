pipeline {
    agent any


    tools {
        jdk 'JDK17'
        nodejs 'node22'
    }

   environment {
        SCANNER_HOME = tool "sonar-scanner"
    }


    stages {

         stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout from SCM') {
            steps {
                git branch: 'master',  url: 'https://github.com/onurglr/devops-05-pipeline-aws-onur.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                 script {
                    if (isUnix()) {
                            sh 'npm install'
                        } else  {
                            bat 'npm install'
                     }
                 }
            }
        }

    
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('SonarTokenForOnurJenkins') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=devops-05-pipeline-aws-onur \
                        -Dsonar.projectKey=devops-05-pipeline-aws-onur
                    '''
                }
            }
        }
   

/*

       stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarTokenForOnurJenkins'
                }
            }
        }

*/

        stage('TRIVY FS SCAN') {
             steps {
                 sh "trivy fs . > trivyfs.txt"
             }
         }


        stage("Docker Build & Push"){
             steps{
                 script{
                   withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                      sh "docker build -t devops-05-pipeline-aws ."
                      sh "docker tag devops-05-pipeline-aws onurguler18/devops-05-pipeline-aws:latest "
                      sh "docker push onurguler18/devops-05-pipeline-aws:latest "
                    }
                }
            }
        }


        stage("TRIVY Image Scan"){
            steps{
                sh "trivy image onurguler18/devops-05-pipeline-aws:latest > trivyimage.txt"
            }
        }

/*
        stage('Deploy to Kubernetes'){
            steps{
                script{
                    dir('kubernetes') {
                      withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubernetes', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                      sh 'kubectl delete --all pods'
                      sh 'kubectl apply -f deployment.yml'
                      sh 'kubectl apply -f service.yml'
                      }
                    }
                }
            }
        }



        stage('Docker Image to Clean') {
            steps {
                // sh 'docker rmi onurguler18/devops-05-pipeline-aws:latest'
                sh 'docker image prune -f'
            }
        }
        */

    }


/*
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'onur18guler@gmail.com',
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
*/

}
