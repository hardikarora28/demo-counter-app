pipeline{
    agent any
    stages{
        stage("Git Checkout"){
            steps{
                git branch: 'main', url: 'https://github.com/hardikarora28/demo-counter-app.git'
            }
        }
        stage("UNIT Testing"){
            steps{
                sh 'mvn test'
            }
        }    
        stage("Integration Testing"){
            steps{
                sh 'mvn verify -DskipUnitTests'
            }        
        }
        stage("Maven Build"){
            steps{
                sh 'mvn clean install'
            }    
        }
        stage("Sonarqube Analysis"){
            steps{
                script{    
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }   
            } 
        }
        stage("Quality Gate Status"){
            steps{
                script{    
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                }
            }   
        } 
        stage("upload war file to nexus"){
            steps{
                script{   
                    def readPomVersion = readMavenPom file: 'pom.xml' 
                    def nexusRepo = readMavenPom.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/Uber.jar', 
                            type: 'jar'
                        ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '20.207.199.180:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demoapp-release', 
                    version: "${readPomVersion.version}"
                }
            }   
        }         
    }
}
