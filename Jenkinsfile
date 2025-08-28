pipeline {
    agent any

    environment {
        // GitHub personal access token stored in Jenkins credentials
        GITHUB_TOKEN = credentials('github-creds')

        // GitHub repo in "owner/repo" format
        GITHUB_REPO = 'Vasanth1602/jenkins_workflow'

        // Python virtual environment path
        VENV = 'venv'
    }

    stages {

        stage('Setup') {
            steps {
                echo '🔧 Setting up Python virtual environment...'
                bat """
                    if not exist %VENV% (
                        python -m venv %VENV%
                    )
                    %VENV%\\Scripts\\pip install --upgrade pip
                    %VENV%\\Scripts\\pip install -r requirements.txt
                """
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running unit tests...'
                bat '%VENV%\\Scripts\\python -m unittest discover -s tests'
            }
        }

        stage('Deploy') {
            when {
                expression {
                    return env.BRANCH_NAME == 'main' && env.CHANGE_ID == null
                }
            }
            steps {
                echo '🚀 Deploying application...'
                bat 'start /B %VENV%\\Scripts\\python app.py'
            }
        }

        stage('Merge PR') {
            when {
                expression {
                    return env.CHANGE_ID != null
                }
            }
            steps {
                script {
                    def prNumber = env.CHANGE_ID
                    echo "✅ Build passed — attempting to merge PR #${prNumber}"

                    // Using curl to merge the PR
                    def mergeResponse = bat(
                        script: """
                            setlocal enabledelayedexpansion
                            set TOKEN=%GITHUB_TOKEN%
                            curl -s -w "%%{http_code}" -o response.json -X PUT ^
                                -H "Authorization: token !TOKEN!" ^
                                -H "Accept: application/vnd.github.v3+json" ^
                                https://api.github.com/repos/${GITHUB_REPO}/pulls/${prNumber}/merge
                        """,
                        returnStdout: true
                    ).trim()

                    def httpCode = mergeResponse[-3..-1]
                    def responseBody = readFile('response.json').trim()

                    echo "HTTP Status: ${httpCode}"
                    echo "Response: ${responseBody}"

                    if (httpCode != '200') {
                        error("❌ Failed to merge PR #${prNumber}. Check response above.")
                    } else {
                        echo "✅ PR #${prNumber} merged successfully."
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Build failed. PR will not be merged."
        }
        success {
            echo "✅ Build completed successfully."
        }
    }
}
