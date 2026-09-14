pipeline {

    agent any

    tools {
        nodejs 'NodeJS-7.8.0'
    }
    
    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
        stage('Lint Dockerfile') {
            steps {
                sh 'docker run --rm -i hadolint/hadolint hadolint - < Dockerfile'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t muratbekov3/nodemain:v1.0 .'
                    }

                    else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t muratbekov3/nodedev:v1.0 .'
                    }

                }
            }
        }
        stage('Scann image with trivy') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {
                        def vulnerabilities = sh(
                            script: " trivy image --exit-code 0 --severity HIGH,MEDIUM,LOW --no-progress muratbekov3/nodemain:v1.0", 
                            returnStdout: true
                        ).trim()
            
                        echo "Vulnerability Report:\n${vulnerabilities}"
                    }

                    else if (env.BRANCH_NAME == 'dev') {
                        def vulnerabilities = sh(
                            script: " trivy image --exit-code 0 --severity HIGH,MEDIUM,LOW --no-progress muratbekov3/nodedev:v1.0", 
                            returnStdout: true
                        ).trim()

                        echo "Vulnerability Report:\n${vulnerabilities}"
                    }

                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {

                    docker.withRegistry(
                        'https://index.docker.io/v1/',
                        'docker-cred'
                    ) {

                        if (env.BRANCH_NAME == 'main') {
                            sh 'docker push muratbekov3/nodemain:v1.0'
                        }

                        else if (env.BRANCH_NAME == 'dev') {
                            sh 'docker push muratbekov3/nodedev:v1.0'
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {

                        sh '''
                            docker pull muratbekov3/nodemain:v1.0

                            docker stop nodemain-container
                            docker rm nodemain-container

                            docker run -d \
                                --name nodemain-container \
                                --expose 3000 \
                                -p 3000:3000 \
                                muratbekov3/nodemain:v1.0
                        '''

                    } else if (env.BRANCH_NAME == 'dev') {

                        sh '''
                            docker pull muratbekov3/nodedev:v1.0

                            docker stop nodedev-container 
                            docker rm nodedev-container 

                            docker run -d \
                                --name nodedev-container \
                                --expose 3001 \
                                -p 3001:3000 \
                                muratbekov3/nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}

