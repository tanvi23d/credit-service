podTemplate(cloud: 'kubernetes',label: 'kubernetes',
            containers: [
                    containerTemplate(name: 'podman', image: 'quay.io/containers/podman', privileged: true, command: 'cat', ttyEnabled: true)
					
            ]) 
{

node{
    def MAVEN_HOME = tool "mymaven"
    env.PATH = "${env.PATH}:${MAVEN_HOME}/bin"

    stage('checkout'){
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/tanvi23d/credit-service.git']]])
    }
    
    stage('compile'){
        sh 'mvn clean compile'
    }
    
     stage('unit testing'){
        sh 'mvn test'
    }
      stage('Code quality analysis') 
	{
		withSonarQubeEnv('tanvisonar') 
		{
                 sh 'mvn sonar:sonar -Dsonar.organization=tanvi23d -Dsonar.projectKey=credit-service1'
		 stash includes: '*', name: 'myproject'

    		}
	 }
	 
	 

    
}
	
node('kubernetes'){
   container('podman') {
	stage('Image Build'){
	   unstash 'myproject'
	   sh 'podman image build -t credit-service .'	
		
	}
   }
}
  
}

