currentBuild.displayName="color_login_app_v13.0-#"+currentBuild.number
pipeline{
    agent any
    options {
      timeout(time: 1, unit: 'MINUTES') 
         }
 
    stages{
        stage("Checkout-SCM")
        {
            steps{
                cleanWs()
                checkout([$class: 'GitSCM',
                branches: [[name: '*/master']], 
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'CleanBeforeCheckout']], 
                submoduleCfg: [], 
                userRemoteConfigs: [[credentialsId: 'github_credentials', 
                url: 'https://github.com/HariReddy910/sonar_Pract.git']]])
                echo "Download finished form SCM"
            }
        }
        stage("Build")
        {
            steps{
                  withSonarQubeEnv('SonarQube') {
                 sh label: '', script: 'mvn package sonar:sonar'
                 echo "archeiving Artifacts" 
                 archiveArtifacts '**/*.war'
                  }     
            }
        }
            stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
       
        stage("Deployment-AppServer"){
            steps{
                echo "hi"
                sh label: '', script: 'scp /var/lib/jenkins/workspace/color_login_app_v13.0/webapp/target/webapp.war ubuntu@172.31.26.148:/var/lib/tomcat9/webapps/color_login_app_v13.0.war'
            }
        }
       
           stage('uploading artifacts to Jfrog artfactory') {
           steps {
              script { 
                 def server = Artifactory.server 'Artifactory-1'
                 def uploadSpec = """{ 
                   "files": [{
                       "pattern": "**/*.war",
                       "target": "Jfrog-harindra-artifactory"
    
                       
                    }]
                 }"""
                  server.upload(uploadSpec) 
     }     }    }  
    }
    
}
