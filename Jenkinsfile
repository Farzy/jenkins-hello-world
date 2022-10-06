/* Requires the Docker Pipeline plugin */
pipeline {
    agent { docker { image 'node:16.17.1-alpine' } }

    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
        VERBOSE      = 'false'
    }

    stages {
        stage('Setup') {
            sh 'apk update && apk add git'
        }
        stage('build') {
            steps {
                sh 'node --version'
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
        stage('env vars') {
            options {
                timestamps()
            }
            steps {
                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"
                sh 'printenv'
            }
        }
        stage('Verbose') {
            when {
                environment name: 'VERBOSE', value: 'true'
            }
            steps {
                echo 'Run PS'
                sh 'ps aux'
            }
        }
        stage('random') {
            steps {
                sh "dd if=/dev/urandom bs=64 count=1 | sha256sum > randomfile.txt"
                sh "dd if=/dev/urandom bs=64 count=10 | sha256sum > randomfile2.txt"
            }
        }
        stage('tagged') {
            environment {
                MYTAG = "${sh(returnStdout: true, script: 'git describe --tags --exact-match || true').trim()}"
            }
            when {
                anyOf {
                    buildingTag()
                    not { equals expected: '', actual: '${MYTAG}' }
                }
            }
            steps {
                echo "BUILDING A TAG: '${TAG_NAME}' or '${MYTAG}'!"
                echo "This stage only executes on tag build.  "
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
