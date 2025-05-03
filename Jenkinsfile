pipeline {
  agent any
  parameters {
    booleanParam(name: 'echo_server', defaultValue: false, description: 'Deploy echo-server?')
    booleanParam(name: 'echo_new_server', defaultValue: false, description: 'Deploy echo-new-server?')
  }
  stages {
    stage("Verify tooling") {
      steps {
        sh '''
          docker version
          docker info
          docker compose version
          curl --version
          jq --version
        '''
      }
    }

    stage('Prune Docker data') {
      steps {
        sh 'docker system prune -a --volumes -f'
      }
    }

    stage('Start container') {
      steps {
        script {
          if (params.echo_server) {
            sh 'docker compose up -d echo-server --no-color --wait'
          }
          if (params.echo_new_server) {
            sh 'docker compose up -d echo-new-server --no-color --wait'
          }
          sh 'docker compose ps'
        }
      }
    }

    stage('Run tests against the container') {
      steps {
        script {
          if (params.echo_server) {
            sh 'curl http://localhost:3000/param?query=demo | jq'
          } else {
            sh 'curl http://localhost:4000/param?query=demo | jq'
          }
        }
      }
    }
  }

  post {
    always {
      sh 'docker compose down --remove-orphans -v'
      sh 'docker compose ps'
    }
  }
}
