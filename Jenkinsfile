pipeline {
  agent any
    stages {
        stage("Compile") {
        steps {
            sh "./gradlew compileJava"
        }
        }
        stage("Unit test") {
        steps {
            sh "./gradlew test"
        }
        }
        stage("Code coverage") {
        steps {
            sh "./gradlew jacocoTestReport"
            publishHTML (target: [
                reportDir: 'build/reports/jacoco/test/html',
                reportFiles: 'index.html',
                reportName: "JaCoCo Report" ])
            sh "./gradlew jacocoTestCoverageVerification"
        }
        }
        stage("Static code analysis") {
        steps {
            sh "./gradlew checkstyleMain"
            publishHTML (target: [
                reportDir: 'build/reports/checkstyle/',
                reportFiles: 'main.html',
                reportName: "Checkstyle Report" ])
        }
        }
        stage("Build") {
            steps {
                sh "./gradlew build"
            }
        }

        stage("Docker build") {
      steps {
        sh "docker build -t 10.0.0.2:5000/calculator:${BUILD_TIMESTAMP} ."
      }
    }
    // stage("Docker login") {
    //   steps {
    //     withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'leszko',
    //                       usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
    //       sh "docker login --username $USERNAME --password $PASSWORD"
    //     }
    //   }
    // }

    stage("Docker push") {
      steps {
        sh "docker push 10.0.0.2:5000/calculator:${BUILD_TIMESTAMP}"
      }
    }
    stage("Deploy to staging") {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: '	5cdc15b2-110d-41cd-bba3-3c881126193a', keyFileVariable: 'KEY')]) {
            sh "ansible-playbook -i inventory/staging playbook.yml --private-key ${KEY} -u vagrant -e tag=${BUILD_TIMESTAMP}"
        }
      }
    }
    }
}