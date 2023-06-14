pipeline {
  agent any
  tools { 
        maven 'Maven_3-5.2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=lateefbuggywebapp -Dsonar.organization=lateefbuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=3bdccbc235209381b05563d2204be7dfd704ad57'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://671907533637.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    

  stage('Kubernetes Deployment of ASG Bugg Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		        sh('kubectl delete all --all -n test')
		        sh ('kubectl apply -f deployment.yaml --namespace=test')
		      }
	      }
   	}

  }
}
