pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=abuggywebapp -Dsonar.organization=abuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=b53fa493b82455e652aab386d23948d3f7fe9072'
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
		sh 'rm -f ~/.dockercfg ~/.docker/config.json || true'
                    docker.withRegistry('https://095531701288.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws_credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
