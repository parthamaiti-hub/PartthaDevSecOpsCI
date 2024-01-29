pipeline {
  agent any
  environment{
        registry = "parthamaiti/numeric-app"
        registryCredential = 'docker-hub'        
    }
  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
       }
       stage('unit test') {
            steps {
              sh "mvn test"
            }
           post {
               always {
                 junit 'target/surefire-reports/*.xml'
                 jacoco execPattern: 'target/jacoco.exec'
               }
            }
                
        } 
        
        /*
        stage('Sonarqube scan SAST') {
            steps {
              sh "mvn clean verify sonar:sonar -Dsonar.projectKey=appsample -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_148b7e3ab3857e43ff35306a582c545fb0ade313"

            }
                           
        } 
        */
        /*
        stage('Vulnerability Scan Dependencies check') {
            steps {
              sh "mvn dependency-check:check"
            }
            post {
               always {
                 dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'       
                 			         
               }
            }
                
        } 
        */
        
        stage('Docker build and Push') {
	      steps{
	        withDockerRegistry([credentialsId: "dockerhub-partha", url: ""]) {
	          sh 'printenv'
	          sh 'docker build -t parthamaiti/samplewebapp1:"test" .'
	          sh 'docker push parthamaiti/samplewebapp1:"test"'
	        }
	      }
	    }
	    
	    stage(' Find kubernetes details') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                sh 'kubectl get all'
            }
            }
        }
	    
	    stage('Kubernetes Deployment -DEV')  {
	      steps{
	        withKubeConfig([credentialsId: 'kubeconfig']) {
	           sh "kubectl apply -f k8s_deployment_service.yaml"
	        }
	      }
	    }
    
          
    }
	post {
    success {
      mail to: "parthamaiti.conn@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Yay, we passed."
    }
    failure {
      mail to: "parthamaiti.conn@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Boo, we failed."
    }
  }
}
