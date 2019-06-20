node{
      def dockerImageName= 'sambadocker99/javademoapp_$JOB_NAME:$BUILD_NUMBER'
      stage('SCM Checkout'){
         git 'https://github.com/yssivareddy/java-groovy-docker.git'
      }
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }       
     
     stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }
      
     stage('Build Docker Image'){
           sh "docker build -t ${dockerImageName} ."
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwdsamba', variable: 'dockerPWD')]) {
              sh "docker login -u sambadocker99 -p ${dockerPWD}"
         }
        sh "docker push ${dockerImageName}"
      }
      
    stage('Run Docker Image'){
            def dockerContainerName = 'javademoapp_$JOB_NAME_$BUILD_NUMBER'
             def changingPermission='sudo chmod +x stopscript.sh'
            def scriptRunner='sudo ./stopscript.sh'           
            def dockerRun= "sudo docker run -p 8082:8080 -d --name ${dockerContainerName} ${dockerImageName}" 
            withCredentials([string(credentialsId: 'deploymentserverpwd', variable: 'dpPWD')]) {
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no devops@18.212.1.168" 
                  sh "sshpass -p ${dpPWD} scp -r stopscript.sh devops@18.212.1.168:/home/devops" 
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no devops@18.212.1.168 ${changingPermission}"
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no devops@18.212.1.168 ${scriptRunner}"
                  sh "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no devops@18.212.1.168 ${dockerRun}"
            }
      
      }
         
  }
      
