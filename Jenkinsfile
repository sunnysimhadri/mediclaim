pipeline {
   agent any
	environment {
       PATH = "/opt/maven3/bin:$PATH"
}
	stages {
      stage('Git Checkout') {
         steps {
            git 'https://github.com/saidevmalik/mediclaim.git'
		}
	}
	stage('Build') {
		steps {
			withSonarQubeEnv('sonar3') {
				sh '/opt/maven3/bin/mvn clean verify sonar:sonar'
			}
		}
	}
	//stage("Quality Gate") {
          //  steps {
            //  timeout(time: 2, unit: 'MINUTES') {
              //  waitForQualityGate abortPipeline: true
             // }
           // }
          //}
		stage("Quality Gate") {
            steps {
                sh 'sleep 5s'
                timeout(time: 5, unit: 'MINUTES') {
                    script {
                        def result = waitForQualityGate()
                        if (result.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${result.status}"
                        } else {
                            echo "Quality gate passed with result: ${result.status}"
                        }
                    }
                }

            }
        }
	stage ('Publish Code Coverage Report')

            { steps {

                   build 'Code Coverage'

            }}      
		stage ('Publish Unit Test Report')

            { steps {

               build 'Test Reports'

            }}   
		stage("email"){
			steps{
		mail bcc: '', body: 'Build is sucessful', cc: '', from: '', replyTo: '', subject: 'Build', to: 'saidevmalik123@gmail.com'
		}
		}
	stage ('Deploy') {
		steps {
			sh '/opt/maven3/bin/mvn clean deploy -Dmaven.test.skip=true'
		}
	}
	stage ('Release') {
		steps {
			sh 'export JENKINS_NODE_COOKIE=dontkillme ;nohup java -jar $WORKSPACE/target/*.jar &'
		}
	}
	
		//stage ('DB Migration') {
		//steps {
		//	sh 'mvn flyway:repair flyway:migrate -P migrations'
		//}
	//}
		stage('Deploye-production'){
			steps{
	sshagent(['tomcat']) {
                       sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/mediclain2/target/mediclaim-0.0.13-SNAPSHOT.jar ec2-user@13.126.15.55:/opt/apache-tomcat-9.0.40/webapps/'
			}
		}
		}
	}
}
