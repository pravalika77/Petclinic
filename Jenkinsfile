pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', url: 'https://github.com/pravalika77/Petclinic.git'
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
        
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
         stage("Build"){
            steps{
                sh " mvn clean package"
            }
        }
        
         stage('Deploy to Elastic Beanstalk') {
            steps {
                script {
                    withAWS(region: "${env.AWS_REGION}", credentials: "${env.AWS_CREDENTIALS_ID}") {
                        // Initialize Elastic Beanstalk application
                        sh 'eb init -p ${env.EB_PLATFORM} ${env.APPLICATION_NAME} --region ${env.AWS_REGION}'
                        
                        // Create the environment if it doesn't exist
                        sh '''
                        eb create ${env.ENVIRONMENT_NAME} || true
                        '''
                        
                        // Deploy the application to Elastic Beanstalk
                        sh 'eb deploy ${env.ENVIRONMENT_NAME}'
                    }
                }
            }
        }
}
