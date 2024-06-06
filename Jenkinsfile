pipeline {
  agent any
    stages {
      stage('List Workspace Contents'){
        steps {
          sh "ls"
        }
      }
      stage('Print Workspace Path'){
        steps {
          sh "pwd"
        }
      }
      stage('Run script.sh'){
        steps {
          sh "sh script.sh"
        }
      }
    }
}
