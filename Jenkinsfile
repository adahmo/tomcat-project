pipeline {
   environment {
     git_url = "https://github.com/adahmo/tomcat-project.git"
     git_branch = "master"
   }

  agent any
 
  stages {
    stage('Pull Source') {
      steps {
        git credentialsId: 'b1c07525-6ca1-44e2-aa83-33b06a4b2ebb', branch: "${git_branch}", url: "${git_url}"
       
      }
     }
    
    stage('Maven Build') {
     steps { 
          sh "if [ -f \"pom.xml\" ];then mvn -B -f pom.xml clean package && cp target/*.jar .;else echo \"This is not a Java Project\";fi"
     }
    }
     
     stage('Docker Image Build') {     
        steps {
              sh 'sudo docker build -t mytomcat-image . '
               }
             }
        stage('Docker image push') {
           steps {
                 withCredentials([usernamePassword(credentialsId: 'dc993465-44a6-4b28-86c2-56e9412bf187', passwordVariable: 'Password', usernameVariable: 'Username')]) {
                 sh "sudo docker login -u ${env.Username} -p ${env.Password}"
                 sh "sudo docker image tag mytomcat-image adamumj/mytomcat-image:test"
                 sh "sudo docker image push adamumj/mytomcat-image:test" 
               } 
             }  
          }
      stage('Deploy app') {
         steps {
            // sh 'kubectl apply -f tomcat-app.yaml'
            sh 'docker run -d --name mycont adamumj/mytomcat-image:test'
            
         }
      }
    }

  post {
    always {
      deleteDir() /* cleanup the workspace */
    }
  }
  }
