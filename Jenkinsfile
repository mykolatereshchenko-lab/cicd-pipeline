pipeline {
    agent any

    environment {
        PORT = "3000"
    }

    stages {

        stage('Set Port') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        env.PORT = "3000"
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.PORT = "3001"
                    }
                }
                echo "Branch: ${env.BRANCH_NAME}, Port: ${env.PORT}"
            }
        }

        stage('Build') {
            steps {
                bat 'npm install'
            }
        }

        stage('Docker Build') {
            steps {
                bat "docker build -t app:%BRANCH_NAME% ."
            }
        }

        stage('Deploy') {
            steps {
                bat """
                docker stop app-%BRANCH_NAME% || exit 0
                docker rm app-%BRANCH_NAME% || exit 0

                docker run -d -p %PORT%:3000 ^
                 -e HOST=0.0.0.0 ^
                 --name app-%BRANCH_NAME% ^
                 app:%BRANCH_NAME%
                """
            }
        }
    }
}
