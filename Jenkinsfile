pipeline {

agent {
    node {
      label 'master'
    }
  }
  
      tools {

        maven 'maven-3.8.1'
    }
    
    stages {
        
     stage('Tool version Check') {
            steps {
                echo "Hello, Maven"
                sh "mvn --version" 
            }
        }
        
    stage('SonarQube Analysis') {
      environment {
        SCANNER_HOME = tool 'sonarqube-4.6.2'
        ORGANIZATION = "scanner"
        PROJECT_NAME = "scanner"
      }
      steps {
        withSonarQubeEnv('sonarqube') {
            sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.organization=$ORGANIZATION \
            -Dsonar.projectKey=$PROJECT_NAME \
            -Dsonar.sources=.'''
        }
      }
    }
        
        stage("Quality Gate") {
         steps {
           timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
          }
         }
       }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        stage('Stage 2') {
            steps {
                echo 'Stage2 Hello world!'  
                echo  'Stage2 $date'
               
            }
            
        }
        
        
        stage('Stage 3') {
            steps {
                echo 'Welcome to Stage3 '  
                echo  'Stage3 '
            }
            
        }           
    }

      post {
       success {
         notifySuccess()
       }
       unstable {
         notifyUnstable()
       }
       failure {
         notifyFailed()
       }
     }
         
}


def notifyBuild(String buildStatus = 'STARTED', String colorCode = '#5492f7', String notify = '') {

  def project = 'freestyleproject'
  def channel = "@manoj.k"
  def base = "https://github.com/devaprabhu1995/demo-tech-start-2.git/${project}/commits/"

  def commit = sh(returnStdout: true, script: 'git log -n 1 --format="%H"').trim()
  def link = "${base}${commit}"
  def shortCommit = commit.take(6)
  def title = sh(returnStdout: true, script: 'git log -n 1 --format="%s"').trim()
  def subject = "<${link}|${shortCommit}> ${title}"

  def summary = "${buildStatus}: Job <${env.RUN_DISPLAY_URL}|${env.JOB_NAME} [${env.BUILD_NUMBER}]>\n${subject} ${notify}"

  slackSend (channel: "${channel}", color: colorCode, message: summary)

}

def author() {
  return sh(returnStdout: true, script: 'git log -n 1 --format="%an" | awk \'{print tolower($1);}\'').trim()
}


def notifyStarted() {
  notifyBuild()
}

def notifySuccess() {
  notifyBuild('SUCCESS', 'good')
}

def notifyUnstable() {
  notifyBuild('UNSTABLE', 'warning', "\nAuthor: @${author()} <${RUN_CHANGES_DISPLAY_URL}|Changelog>")
}

def notifyFailed() {
  notifyBuild('FAILED', 'danger', "\nAuthor: @${author()} <${RUN_CHANGES_DISPLAY_URL}|Changelog>")
}
