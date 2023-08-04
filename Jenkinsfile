pipeline{ 
    agent{label 'spcon'}
    tools{ 
        jdk 'JDK_17'
        maven 'maven-3.9.3'
    }
    stages{
        stage ('source code') {
            steps{
                git url: 'https://github.com/Rahulvelamasetty/spring-petclinic.git',
                    branch: 'main'
            }
        }
        stage ('build & package'){
            steps{
                sh 'cd /home/ubuntu/workspace/spcon'
                sh 'mvn package'
            }
        }
        stage ('test') {
            steps {
                archiveArtifacts artifacts: '**/target/spring-petclinic-3.1.0-SNAPSHOT.jar',
                                  onlyIfSuccessful: true
                junit testResults: '**/surefire-reports/TEST-*.xml'
                
            }
        }
        stage ('Artifactory configuration'){
            steps{ 
                rtServer ( 
                   id: 'JFROG_CLOUD',
                   url: 'https://rahul14.jfrog.io/artifactory',
                   credentialsId: 'jfrog_jenkins'
                ) 
                rtMavenDeployer ( 
                    id: 'spc_deployer',
                    serverId: 'JFROG_CLOUD',
                    releaseRepo: 'app-spc-libs-release',
                    snapshotRepo: 'app-spc-libs-snapshot'

                )
                rtMavenRun( 
                    tool: 'maven-3.9.3',
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: 'spc_deployer' 
                )
                rtPublishBuildInfo( 
                    serverId: 'JFROG_CLOUD'
                )
                
            }
        }
        stage ( 'sonarqube analysis'){
            steps{ 
                withSonarQubeEnv('sonar_cloud'){ 
                    sh 'mvn clean package sonar:sonar -Dsonar.organization=khajaprojectsjuly23 -Dsonar.token=70716859b54ed25180686bf3ea4938b3fc2301be -Dsonar.projectKey=springpetclinic'
                }
            }
        }
        
    }   
}