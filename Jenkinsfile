def dockerImage

pipeline {
    agent any

    environment {
        gcpCreds = 'gcp_credentials'
        dockerCreds = credentials('dockerhub_login')
        registry = "botaaron/vatcal"
        registryCredentials = "dockerhub_login"
        TF_VAR_gcp_project = "qwiklabs-gcp-01-a2c7ef816009"
        TF_VAR_docker_registry = "${registry}"
    }

    stages {
        stage('Run Tests') {
            steps {
                sh 'npm install'
                sh 'CI=true npm test'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build(registry)
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry("", registryCredentials) {
                        dockerImage.push("${env.BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Clean Up') {
            steps {
                sh "docker image prune --all --force --filter 'until=48h'"
            }
        }

        stage('Provision Server') {
            steps {
                script {
                    withCredentials([file(credentialsId: gcpCreds, variable: 'GCP_CREDENTIALS')]) {
                        sh '''
                            export GOOGLE_APPLICATION_CREDENTIALS=$GCP_CREDENTIALS
                            terraform init
			    terrascan scan -i terraform -t gcp
                            terraform apply -auto-approve
                        '''
                    }
                }
            }
        }
    }
}
