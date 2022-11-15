node{
   stage('SCM Checkout'){
     git 'https://github.com/Madhusrinivasaperumal/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('Build Docker Image'){
   sh 'docker build -t aksharasrimadhu/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u aksharasrimadhu -p ${dockerPassword}"
    }
   sh 'docker push aksharasrimadhu/myweb:0.0.2'
   } 
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 65.1.91.193:8083"
   sh "docker tag aksharasrimadhu/myweb:0.0.2 65.1.91.193:8083/akshu:1.0.0"
   sh 'docker push 65.1.91.193:8083/akshu:1.0.0'
   }  	
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f akshutest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name akshutest aksharasrimadhu/myweb:0.0.2' 
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
  } 
  }
  }
