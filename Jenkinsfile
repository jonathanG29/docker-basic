pipeline {
    agent any
    
    tools {
        dotnetsdk 'dotnet-9.0.203'
        nodejs 'node-20.19.2'
    }

    environment {
        PATH = "${env.PATH}:/opt/dotnet-tools" // 🔧 Se actualizó para incluir la nueva ruta
        SONARQUBE_URL = 'http://192.168.1.5:9000'
        SONAR_TOKEN = 'sqp_8e2829e1a23516d875b04e9747633d0ad5f8f41e'
    }

    stages {

        stage('Check versions') {
            steps {
                script {
                    echo 'Node.js version:'
                }
            }
        }

        stage('Checkout') {
            steps {
                git url: 'https://github.com/jonathanG29/docker-basic.git', branch: 'main'
            }
        }

        stage('Backend - Restore') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Restoring dependencies...'
                    sh 'dotnet restore'
                }
            }
        }

        stage('Backend - Static Analysis') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Running static analysis...'
                    sh '''
                        /opt/dotnet-tools/dotnet-sonarscanner begin \ // 🔧 Ruta cambiada
                            /k:"Docker-Basic" \
                            /d:sonar.host.url=${SONARQUBE_URL} \
                            /d:sonar.login=${SONAR_TOKEN}

                        dotnet build

                        /opt/dotnet-tools/dotnet-sonarscanner end \   // 🔧 Ruta cambiada
                            /d:sonar.login=${SONAR_TOKEN}
                    '''
                }
            }
        }

        stage('Backend - Code Coverage') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Running code coverage...'
                    sh 'dotnet test --collect:"XPlat Code Coverage" --no-build --verbosity normal'
                }
            }
        }

        stage('Backend - Build') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Building the project...'
                    sh 'dotnet build --configuration Release --no-restore'
                }
            }
        }

        stage('Backend - Publish') {
            steps {
                dir('10-net9-remix-pg-env/Backend') {
                    echo 'Publishing the project...'
                    sh 'dotnet publish --configuration Release --no-build -o ./publish'
                }
            }
        }

        stage('Frontend - Install') {
            steps {
                dir('10-net9-remix-pg-env/Frontend') {
                    echo 'Installing dependencies...'
                    sh 'npm install'
                }
            }
        }

        stage('Frontend - Test') {
            steps {
                dir('10-net9-remix-pg-env/Frontend') {
                    echo 'Running tests...'
                    sh 'npm test'
                }
            }
        }

        stage('Frontend - Build') {
            steps {
                dir('10-net9-remix-pg-env/Frontend') {
                    echo 'Building the project...'
                    sh 'npm run build'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed'
            cleanWs()
        }
        success {
            echo 'Pipeline executed successfully'
        }
        failure {
            echo 'Pipeline execution failed'
        }
    }
}
