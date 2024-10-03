pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
		git branch: 'main', poll: false, url: 'https://github.com/Nelztacy/pet-clinic-app.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
  
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
         stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
        stage("Docker Build & Push"){
            steps{
                script{
		   withDockerRegistry(credentialsId: 'DockerHub-Cred') {
                        sh "docker build -t image1 ."
                        sh "docker tag image1 nelzone/pet-clinic-app:latest "
                        sh "docker push nelzone/pet-clinic-app:latest "
                    }
                }
            }
        }
        
        stage("TRIVY"){
            steps{
                sh " trivy image nelzone/pet-clinic-app:latest"
            }
        }
        
        stage("Deploy To Tomcat"){
            steps{
                sh "cp  /var/lib/jenkins/workspace/CI-CD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
            }
        }
    }
}
