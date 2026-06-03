pipeline {
    agent any
 
    stages {
        stage('Checkout'){
            steps {
              git url: 'https://github.com/BOTaaron/lbg-vat-calculator',
                  branch: 'main'

            }
        }
        stage('Build') {
            steps {
              sh 'npm install'

              sh 'npm run build'
           }
        }
        stage('Archive') {
            steps {
              sh 'tar -czf build.tar.gz build'
              archiveArtifacts 'build.tar.gz'
            }
        }
    }
}
