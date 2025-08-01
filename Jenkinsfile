pipeline {
    agent any

    environment {
        CI = false // do not treat errors as warnings
        SONARSCANNER = 'sonarscanner'
    }

    stages {
        stage('Run SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarscanner') {
                    script {
                        sh """
                            ${SONARSCANNER} \
                            -Dsonar.projectKey=emmy-coming-soon \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Installing dependencies and building'
                sh 'docker build -t emmy-coming-soon:${BUILD_NUMBER} .'
            }
        }

        stage('Deployment') {
            steps {
                echo 'Deploying to Dockerhub'
                sh 'docker tag emmy-coming-soon:${BUILD_NUMBER} ${USERNAME}/emmy-coming-soon:${BUILD_NUMBER}'
                sh 'docker login -u ${USERNAME} -p ${PASSWORD} docker.io'
                sh 'docker push ${USERNAME}/emmy-coming-soon:${BUILD_NUMBER}'
            }
        }
    }

    post {
        always {
            echo 'Post build actions running'
            // e.g., clean workspace, notify slack, archive artifacts, etc.
        }
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
