
pipeline {
    agent { label 'podman' }

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
                sh 'podman build -t ops300-test:1.0 .'
            }
        }

        stage('Push Image') {
            steps {
                sh 'podman tag localhost/ops300-test:1.0 registry.assign.smahaman-laminou.300.ops:5000/ops300-test:1.0'
                sh 'podman push registry.assign.smahaman-laminou.300.ops:5000/ops300-test:1.0'
            }
        }
    }
}
