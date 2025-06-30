pipeline{
    agent any

    stages{

        stage('Checout Code'){
            steps{
                script{
                    checkout scm
                }
            }
        }

        stage('Dependencies Install'){
            steps{
                script{
                    sh 'npm install'
                }
            }
            
        }

        stage('Run App'){
            steps{
                script{
                    // Stop any previous instance of the app
                    sh 'pkill -f "node app.js" || true'

                    // Start the app in the background and redirect logs
                    sh 'nohup node app.js > app.log 2>&1 &'

                    // Wait a few seconds to ensure the app starts
                    sleep 60

                    // Check if the app is running
                    sh 'curl http://localhost:3001 || echo "App is not responding!"'
                }
            }
        }
    }

    post{
        always{
            cleanWs(cleanWhenNotBuilt: false,
                deleteDirs: true,
                disableDeferredWipeout: true,
                notFailBuild: true,
                patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                            [pattern: '.propsfile', type: 'EXCLUDE']])
        }
    }
}
