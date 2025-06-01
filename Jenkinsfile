pipeline {
    agent any
    environment{
        APP_VERSION = "1.0.${BUILD_ID}"
    }
    stages {
        stage('checkout') {
            steps {
                script {
                    // Checkout Frontend Repository
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'https://github.com/timgio26/customerDataPlatform_react.git']],
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'frontend']]
                    ])
                    // Checkout Backend Repository
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: 'main']],
                        userRemoteConfigs: [[url: 'https://github.com/timgio26/customerDataPlatform_mongoDb.git']],
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'backend']]
                    ])
                }
            }
        }
        stage('test') {
            steps {
                bat '''
                cd frontend
                npm i
                npm test
                '''
            }
        }
        stage('build run on local') {
            steps {
                bat'''
                dir
                docker build -t my-jenkins .
                docker-compose up -d
                '''
            }
        }
    }
    post{
        success{
            echo 'open http://localhost:3000/'
            bat'''
            echo last success build : %APP_VERSION% > lastsuccessbuild.txt
            '''
            mail bcc: '', body: "pipeline running successfuly ${APP_VERSION }", cc: '', from: '', replyTo: '', subject: 'jenkins pipeline result', to: 't.giovandi.523@studms.ug.edu.pl'
        }
        failure{
            bat'''
            echo fail pipeline please refer to:
            more lastsuccessbuild.txt
            '''
        }
    }
}

