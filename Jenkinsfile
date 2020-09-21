node{
   stage('SCM Checkout'){
     git 'https://github.com/BharaniDharanBabuN/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('Build Docker Image'){
   sh 'docker build -t bh0272/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u bh0272 -p ${dockerPassword}"
    }
   sh 'docker push bh0272/myweb:0.0.2'
   }
   stage ('Remove Existing Container'){
   try{
    sh 'docker rm -f tomcattest'
	}
	catch(error){
	      // do nothing if there is an exception
		  }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest bh0272/myweb:0.0.2' 
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 15.207.117.247:8083"
   sh "docker tag bh0272/myweb:0.0.2 15.207.117.247:8083/bharani:1.0.0"
   sh 'docker push 15.207.117.247:8083/bharani:1.0.0'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    } 
}
}
