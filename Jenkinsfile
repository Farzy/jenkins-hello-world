/* Requires the Docker Pipeline plugin */
pipeline {
    agent { docker { image 'node:16.17.1-alpine' } }

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }

    stages {
        stage('build') {
            steps {
                sh 'node --version'
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
                echo 'Run PS'
                sh 'ps aux'
            }
        }
        stage('env vars') {
            steps {
                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"
                sh 'printenv'
            }
        }
        stage('random') {
            steps {
                sh "dd if=/dev/urandom bs=64 count=1 | sha256sum > randomfile.txt"
                sh "dd if=/dev/urandom bs=64 count=10 | sha256sum > randomfile2.txt"
            }
        }
    }
    
    post {
        always {
            echo 'POST: This will always run'
            archiveArtifacts artifacts: 'randomfile*.txt', fingerprint: true
        }
        success {
            echo 'POST: This will run only if successful'
        }
        failure {
            echo 'POST: This will run only if failed'
        }
        unstable {
            echo 'POST: This will run only if the run was marked as unstable'
        }
        changed {
            echo 'POST: This will run only if the state of the Pipeline has changed'
            echo 'POST: For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
