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
        stage('Trigger Deployment') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {

                        build job: 'Deploy_to_main',
                              wait: true

                    }

                    else if (env.BRANCH_NAME == 'dev') {

                        build job: 'Deploy_to_dev',
                              wait: true
                    }
                }
            }
        }

        // stage('Deploy') {
        //     steps {
        //         script {

        //             if (env.BRANCH_NAME == 'main') {

        //                 sh '''
        //                     docker stop nodemain-container || true
        //                     docker rm nodemain-container || true

        //                     docker run -d \
        //                         --name nodemain-container \
        //                         --expose 3000 \
        //                         -p 3000:3000 \
        //                         nodemain:v1.0
        //                 '''

        //             }

        //             else if (env.BRANCH_NAME == 'dev') {

        //                 sh '''
        //                     docker stop nodedev-container || true
        //                     docker rm nodedev-container || true

        //                     docker run -d \
        //                         --name nodedev-container \
        //                         --expose 3001 \
        //                         -p 3001:3000 \
        //                         nodedev:v1.0
        //                 '''
        //             }
        //         }
        //     }
        // }
    }
}