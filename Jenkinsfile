pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:tejasjoshi23'
       appRegistry = "603530742127.dkr.ecr.ap-south-1.amazonaws.com/capstoneproject-202051091"
       capstoneRegistry = "https://603530742127.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "CapstoneProject_202051091"
        service = "myServices"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Aryanhac/Capstone-Project'
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
            withAWS(credentials: 'tejasjoshi23', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
