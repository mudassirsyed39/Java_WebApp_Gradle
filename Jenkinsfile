currentBuild.displayName = "Spring_gradle # "+currentBuild.number
        
pipeline{
        agent any  
        environment { 
            VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
            }
        
        stages{
              stage('Quality Gate Statuc Check'){

               agent {
                  docker {
                  image 'openjdk:11'
                }
            }
                steps{
                  script{
                    withSonarQubeEnv('sonarserver') { 
                      sh "chmod +x gradlew"
                      sh "java -version"
                      sh "./gradlew sonarqube"
                       }
                      timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                  }
                }  
              }
		    stage('docker image creation stage'){
                steps{
                    script{
                        withCredentials([string(credentialsId: 'docker_password', variable: 'docker_password')]) {
			
                        sh '''
                        docker build -t 34.125.27.120:8083/springapp:${VERSION} .
                        docker login -u admin -p $docker_password 34.125.251.15:8083
                        docker push 34.125.27.120:8083/springapp:${VERSION}
                        ''' 
                        }
                    }
                }

            }
	  	
        }
    }
