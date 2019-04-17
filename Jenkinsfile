pipeline {
  agent any

  triggers {
    cron('H/20 * * * *')
  }

  stages {

    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv('sonar') {
          script {
            def sonarqubeScannerHome = tool name: 'sonar'
            sh script: "${sonarqubeScannerHome}/bin/sonar-scanner -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=admin -Dsonar.password=admin  -Dsonar.projectName=doaApi -Dsonar.projectVersion=1 -Dsonar.projectKey=doaApi -Dsonar.sources=.", returnStatus: true
          }
        }
      }
    }

    stage('Clear Container') {
      steps {
        script {
          sh '''
            docker rm -f $BRANCH_NAME-api1 || true
          '''
        }
      }
    }

    stage ('Build Docker image') {
      steps {

        sh 'docker build -t $BRANCH_NAME-api .'
      }
    }

    stage('Deploy') {
      steps {
        sh 'docker run -d -p $PORT:8080 --name $BRANCH_NAME-api1 $BRANCH_NAME-api'
      }
    }

    stage('wait for services up') {
      steps {
        timeout(time: 2) {
          waitUntil() {
            script {
              def r = sh script: 'curl http://10.46.20.21:8180 | grep Hello; VAR=$?; exit $VAR', returnStatus: true
              return (r == 0)
            }
          }
        }
      }
    }

    stage('functional test') {
      steps {
        sh 'echo functional test'
      }
    }

  }

  environment {
    PORT = '8180'
    IP = '10.46.20.21'
    DB_ROOT_PASSWORD = 'password'
    DB_NAME = 'wordpress'
    BASEPATH = '/vagrant/jenkins_home/workspace/'
  }

}
