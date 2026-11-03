pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Job: ${env.JOB_NAME}"
                echo "Build: ${env.BUILD_NUMBER}"

                sh '''
                    set -eu

                    echo "Preparing build environment"
                    echo "Workspace: $WORKSPACE"

                    rm -rf .venv build dist

                    python3 --version
                    python3 -m venv .venv

                    .venv/bin/python -m pip install --upgrade pip
                    .venv/bin/python -m pip install -r requirements-dev.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    set -eu

                    echo "Running automated tests"

                    .venv/bin/python -m pytest -v
                '''
            }
        }

        stage('Package') {
            steps {
                sh '''
                    set -eu

                    echo "Building package"

                    .venv/bin/python -m build

                    test -d dist

                    WHEEL_FILE="$(find dist -maxdepth 1 -type f -name '*.whl' -print -quit)"

                    if [ -z "$WHEEL_FILE" ]; then
                        echo "ERROR: Expected wheel artifact was not generated."
                        exit 1
                    fi

                    echo "Generated wheel:"
                    echo "$WHEEL_FILE"

                    ls -lh dist/
                '''

                archiveArtifacts artifacts: 'dist/*', fingerprint: true
            }
        }
    }
}
