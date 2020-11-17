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
		stage ('Build'){
       steps {
             sh 'mvn clean install'
}
}
		stage('SonarAnalysis')

    {steps {

       withSonarQubeEnv('sonar') {

           sh 'mvn sonar:sonar'   
      }
    }
 }
 stage('Publish Test Coverage Report') {
   steps {
      step([$class: 'JacocoPublisher', 
           execPattern: '**/build/jacoco/*.exec',
           classPattern: '**/build/classes',
           sourcePattern: 'src/main/java',
           exclusionPattern: 'src/test*'
           ])
          }
      }
	
}
}
