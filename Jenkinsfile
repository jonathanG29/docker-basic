pipeline {
    agent any
    
    tools {
        dotnetsdk 'dotnet-9.0.203'
        nodejs 'node-20.19.2'
    }
    
    environment {
        DOTNET_ROOT = "${env.PATH}:${tool 'dotnet-9.0.203'}/bin"
        PATH = "${env.PATH}:${tool 'node-20.19.2'}/bin"
    }
    
    stages {
        stage('Check versions') {
            steps {
                script {
                    echo 'Node.js version:'
                    sh 'node -v'
                    echo 'NPM version:'
                    sh 'npm -v'
                    echo 'Dotnet version:'
                    sh 'dotnet --version'
                }
            }
        }
    }

    post {
        always {
            echo 'This will always run after the stages.'
        }
    }
}
