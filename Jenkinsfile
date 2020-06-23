node {
    checkout scm

    try {
        stage ('Run unit/integration test') {
            sh label: '', script: 'make test'

            stage ('Build application artifacts') {
                sh label: '', script: 'make build'
            }

            stage ('Create release environmnet and run acceptance tests'){
                sh label: '', script: 'make release'
            }

            stage ('Tag and publish release image'){
                sh label: '', script: 'make tag latest \$(git rev-parse --short HEAD) \$(git tag --points-at HEAD)'
                sh label: '', script: 'make buildtag master \$(git tag --points-at HEAD)'

                withEnv(["DOCKER_USER=${DOCKER_USER}",
                        "DOCKER_PASSWORD=${DOCKER_PASSWORD}"])
                    {
                        sh label: '', script: 'make login'
                    }

                sh label: '', script: 'make publish'
            }
        }
    }
    finally{
        stage ('Collect test reports') {
            step([$class: 'JUnitResultArchiver', testResults: '**/reports/*.xml'])

            stage ('Clean up'){
                sh label: '', script: 'make clean'
                sh label: '', script: 'make logout'
            }
        }
    }
}
