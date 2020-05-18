//libraries {
//  lib('pipeline-library-demo')
//}
@Library('pipeline-library-demo')_

pipeline {
    environment {
    registry = "arunsara/spring-application"
    registryCredential = 'Dockerhub'
  }     
   agent any

   stages {
      stage('checkout') {
         steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/arunsaravana/spring-framework-petclinic.git']]])
		 }
      }
         
      stage ('Build') {
           steps {
                sh 'mvn clean install' 
           } 
      build 'Dave'
      }
      stage ('junit') {
          steps
          {
              junit '**/target/surefire-reports/*.xml'
          }
      }
      stage('SonarQube analysis') {
          
          steps {
            sh 'mvn sonar:sonar'
          }
    
        }
        
        stage('Building docker Image')
        {
            steps {
            
            sh 'docker build . -t arunsara/spring-application:petclinic-v${BUILD_NUMBER}'
        }
        }
        
        stage('Pusing Image to Dockerhub')
        {
            steps {
                sh 'docker push arunsara/spring-application:petclinic-v${BUILD_NUMBER}'
                sh 'docker rmi arunsara/spring-application:petclinic-v${BUILD_NUMBER}'
            }
        }
        stage('ESK Deloy') {
			steps {	
			    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'awstest', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
              echo "Login Successfull"
              sh 'aws eks --region us-west-2  update-kubeconfig --name $ekscluster'
              sh 'sed -i s/"BUILD_NUMBER"/"v${BUILD_NUMBER}"/g app-deployment.yaml'
              sh '/usr/local/bin/kubectl apply -f app-deployment.yaml'
              sh '/usr/local/bin/kubectl apply -f app-service.yaml'
              sh '/usr/local/bin/kubectl get svc'
            }
      }
	  }
      
          }
      }
