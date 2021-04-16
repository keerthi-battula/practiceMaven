pipeline{
  //agent none
  agent any;
     tools{
       maven 'maven'
       jdk 'JDK11'
   }
  
  options
  {
   timeout(time: 15, unit: 'MINUTES') 
    timestamps()
  }
  triggers {
        pollSCM '* * * * *'
    }
  stages
  {
    stage('Example') {
            input {
                message "Should we continue?"
                ok "Yes, we should."
            }
      steps {
        script{
                last_staged = env.STAGE_NAME
                }
                echo "Hello,  nice to meet you."
      }
    }
    stage("build")
    {
   // agent {
 //docker
      //{
      //  image 'maven:3.8-jdk-11'
       // args '-v /root/.m2:/root/.m2'
      //}
   // }
      steps
      {
        script{
                last_staged = env.STAGE_NAME
                }
      bat 'mvn compile'
      }
       post
    {
      success
      {
        echo "build is successful!"
      }

    }
    }
   stage ('package')
    {
     // agent {
// docker
    //  {
       // image 'maven:3.8-jdk-11'
       // args '-v /root/.m2:/root/.m2'
     // }
   // }
      steps
      {
        script{
                last_staged = env.STAGE_NAME
                }
      bat 'mvn package'
      }
       post
    {
      fixed
      {
        echo "package is successful!"
      }

    }
    }
    stage ('sonar bulid and analysis')
    {
     // agent {
// docker
     // {
     //   image 'maven:3.8-jdk-11'
     //   args '-v /root/.m2:/root/.m2'
     // }
  //  }
      steps
      {
        script{
                last_staged = env.STAGE_NAME
                }
        withSonarQubeEnv('sonarSpring') {
                bat 'java -version'
                bat 'mvn clean package sonar:sonar'
              }
      }
    }
  stage("Quality Gate") {
            steps {
              script{
                last_staged = env.STAGE_NAME
                }
              timeout(time: 1, unit: 'HOURS') {
                script{
                
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK')
                        {
                            error "Pipeline failed due to gate failure "
                            
                         }
                    }
              }
            }
  }
    stage('deploy to artifactory'){
            steps{
            script{
                last_staged = env.STAGE_NAME
                }
                rtUpload (
            serverId: 'ARTIFACTORY_SERVER',
            spec: '''{
                 "files": [
                             {
                                "pattern": "target/*.jar",
                                "target": "art-doc-dev-loco/java/"
                            }
                        ]
            }''',
            )
            }
        }
        
        stage('download artifact'){
            steps{
            script{
                last_staged = env.STAGE_NAME
                }
                 rtDownload (
                 serverId: "ARTIFACTORY_SERVER",
                spec:"""{
                     "files": [
                                {
                                    "pattern": "art-doc-dev-loco/java/**",
                                    "target": "artifacts/"      
                                }
                            ]
              }"""
            )
            
            }
        }
    } 
  post 
  {
    always
    {
      echo "runs the steps regardless of completion status!!"
    }
    fixed
    {
echo "previous build is failed and current build is successful!!"
     }
    changed
    {
      echo "the current pipeline has different completion status compared to pervious!!"
    }
    regression
    {
      echo "the current pipeline status is failed or unstable or aborted and previous run was successful!"
    }
    aborted
    {
      echo "the current pipeline is aborted!"
    }
    failure
    {
      echo "pipeline running is failed!"
      mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Stage Name: $last_staged <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'net2bks@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "net2bks@gmail.com";
    }
    success
    {
      echo "pipeline running is successful!"
      echo "========Deploying executed successfully========"
            mail bcc: '', body: 'deploying is sucesfull', cc: '', from: '', replyTo: '', subject: 'deployed', to: 'net2bks@gmail.com'
      emailext attachLog: true, body: "<b>Example</b><br>Project: ${env.JOB_NAME}", from: 'net2bks@gmail.com', mimeType: 'text/html', replyTo: '', subject: "Deploy Success CI: Project name -> ${env.JOB_NAME}", to: "net2bks@gmail.com";
    }
    unstable
    {
      echo "the pipeline run has unstable status caused by test failure code violations"
    }
    cleanup
    {
      echo "if previous posts are excuted then only this will be executed!"
    }
  }
}
