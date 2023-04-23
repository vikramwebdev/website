pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:202052346'
       appRegistry = "886403917421.dkr.ecr.us-east-1.amazonaws.com/202052346_capstone_project"
       capstoneRegistry = "http://886403917421.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "DevCluster"
        service = "202052346_capstone_project"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/vikramwebdev/website.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202052346', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
    }
