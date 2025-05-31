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
            echo {id: $APP_VERSION} > lastsuccessbuild.json
            '''
        }
        failure{
            echo 'rollback deployment'
        }
    }
}

