def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline {
	agent any
	tools{
		maven "MAVEN3"
		jdk "oracleJDK11"
	}
	
	environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "nexus.clouddevhub.com:8081"
        NEXUS_REPOSITORY = "vprofile-repo"
		NEXUS_REPO_ID    = "vprofile-repo"
        NEXUS_CREDENTIAL_ID = "nexuslogin"
        ARTVERSION = "${env.BUILD_ID}"
    }
	

	
	stages{
	
		stage('Print error'){
            steps{
                sh 'fake comment'
            }
        }
		
		stage('Fetch Code'){
			steps {
				git branch: 'vp-rem', url:'https://github.com/kudidev/vprofile-project.git'
			}
		}
		
		stage('BUILD'){
			steps {
				sh 'mvn clean install -DskipTests'
			}
			
			post  {
				success {
					echo 'Now Archiving..'
					archiveArtifacts artifacts: '**/target/*.war'
				}
			}
		}
		
		stage('UNIT TEST'){
			steps{
				sh 'mvn test' 
			}
		
		}
		
		stage('Checkstyle Analysis'){
			steps{
				sh 'mvn checkstyle:checkstyle' 
			}
		
		}
		
		stage ('Sonar Analysis'){
			environment {
				scannerHome = tool 'sonar4.7'    
				
			}
			
			steps {
				withSonarQubeEnv('Sonarqube'){
					 sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
					-Dsonar.projectName=vprofile \
					-Dsonar.projectVersion=1.0 \
					-Dsonar.sources=src/ \
					-Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
					-Dsonar.junit.reportsPath=target/surefire-reports/ \
					-Dsonar.jacoco.reportsPath=target/jacoco.exec \
					-Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
				}	
			}
		}
		
		stage ("Quality Gate"){
			steps {
				timeout(time: 1, unit: 'HOURS') {
					waitForQualityGate abortPipeline: true
				
				}
			
			}
		}
		
        stage("UploadArtifact"){
            steps{
                nexusArtifactUploader(
                  nexusVersion: 'nexus3',
                  protocol: 'http',
                  nexusUrl: 'nexus.clouddevhub.com:8081',
                  groupId: 'QA',
                  version: "Job-0${env.BUILD_ID}-[${env.BUILD_TIMESTAMP}]",
                  repository: 'vprofile-repo',
                  credentialsId: 'nexuslogin',
                  artifacts: [
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
    ]
 )
            }
        


    }
		
		
	}
	post {
      always {
        echo 'Slack Notifications.'
			slackSend channel: '#jenkinscicd',
            color: COLOR_MAP[currentBuild.currentResult],
             message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }
}
