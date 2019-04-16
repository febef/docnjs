pipeline {
  agent any
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
        sleep 1
        sh '''
          docker rm -f dev-API
        '''
      }
    }

    stage('DB Dump & env') {
      steps {
        sh '''
          sed -i "s;8080;8180;g" Dockerfile)
        '''
      }
    }

    stage ('Build image') {
      steps {
        sh 'docker build -t dev-api'
      }
    }

    stage('Deploy') {
      steps {
        sh 'docker run -d -p 8080:8180 --name dev-api1 dev-api'
      }
    }

    stage('wait for services up') {
      steps {
        timeout(time: 5) {
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
    PORT = '82'
    IP = '10.46.20.21'
    DB_ROOT_PASSWORD = 'password'
    DB_NAME = 'wordpress'
    BASEPATH = '/vagrant/jenkins_home/workspace/'
  }

}
