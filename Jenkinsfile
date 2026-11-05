
pipeline {
    agent { label 'podman' }

    triggers {
        pollSCM('H/5 * * * *')
    }

    environment {
        REGISTRY = 'registry.assign.smahaman-laminou.300.ops:5000'
        IMAGE = 'bind-secondary'
    }

    stages {
        stage('Test Agent') {
            steps {
                sh 'hostname'
                sh 'whoami'
                sh 'podman --version'
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                    podman build \
                      -t "${IMAGE}:${BUILD_NUMBER}" .
                '''
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'registry-credentials',
                        usernameVariable: 'REG_USER',
                        passwordVariable: 'REG_PASS'
                    )
                ]) {
                    sh '''
                        set -e
                        export REGISTRY_AUTH_FILE="$(mktemp)"
                        trap 'rm -f "$REGISTRY_AUTH_FILE"' EXIT

                        echo "$REG_PASS" | podman login \
                          --username "$REG_USER" \
                          --password-stdin \
                          "$REGISTRY"

                        podman tag \
                          "${IMAGE}:${BUILD_NUMBER}" \
                          "${REGISTRY}/${IMAGE}:${BUILD_NUMBER}"

                        podman push \
                          "${REGISTRY}/${IMAGE}:${BUILD_NUMBER}"

                        podman logout "$REGISTRY"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'OPS300: BIND9 image built and pushed successfully.'
        }

        failure {
            echo 'OPS300: Pipeline failed. Check console output.'
        }
    }
}
