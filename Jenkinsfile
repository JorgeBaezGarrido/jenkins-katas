pipeline {
  agent any
  stages {
    stage('clone down') {
      steps {
        stash(excludes: '.git', name: 'code')
      }
    }

    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

        stage('test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
  post {
    always {
      deleteDir()
    }

  }
}