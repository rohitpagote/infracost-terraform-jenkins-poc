pipeline {

    agent any

    environment {
        INFRACOST_VCS_PROVIDER = 'github'
        INFRACOST_VCS_REPOSITORY_URL = 'https://github.com/rohitpagote/infracost-terraform-jenkins-poc.git'
        INFRACOST_VCS_PULL_REQUEST_URL = 'https://github.com/rohitpagote/infracost-terraform-jenkins-poc/pull/3'
        // INFRACOST_VCS_PULL_REQUEST_AUTHOR = 'Rohit Pagote'
        // INFRACOST_VCS_PULL_REQUEST_TITLE = 'Change instance type'
        INFRACOST_VCS_BRANCH = 'master'
        INFRACOST_VCS_BASE_BRANCH = 'master'
        // INFRACOST_VCS_COMMIT_SHA = '3ab6d626f5172b205c1a068dbaf346b23bf94e20'
    }

    stages {
        stage('Infracost Version') {
            steps {
                script {
                    try {
                        getInfracostVersion()
                    } catch (exc) {
                        echo "Error occurred at infracost version stage}"
                        throw exc
                    }
                }
            }
        }

        stage('Checkout') {
            steps {
                script {
                    try {
                        doCheckout()
                    } catch (exc) {
                        echo "Error occurred at checkout stage"
                        throw exc
                    }
                }
            }
        }

        stage('Breakdown') {
            steps {
                script {
                    try {
                        doBreakDown()
                    } catch (exc) {
                        echo "Error occurred at breakdown stage"
                        throw exc
                    }
                }
            }
        }
    }
    post {
        cleanup {
            echo '=== performing cleanup'
            deleteDir()
        }
    }
}

def getInfracostVersion() {
    script {
        sh "echo 'Getting infracost version'"
        sh 'infracost --version'
    }
}

def doCheckout() {
    script {
        sh "echo 'Checking out given branch…'"

        createEnv()

        sh("""#!/bin/bash
            cd infracost
            ls -al
        """)

        checkout scm
    }
}

def createEnv() {
    createDeploymentDir()

    sh("""
        echo "=== removing tfvars file if any"
        rm -f common.auto.tfvars deployment.auto.tfvars provider.env

        echo "=== print folder contents"
        pwd
        ls -al
    """)
}

def createDeploymentDir() {
    script {
        sh('''
            mkdir -p infracost/.aws
        ''')
    }
}

def doBreakDown() {
    script {
        sh("""
            pwd;
            echo "=== Cloning the git repo ===";
            git clone https://github.com/rohitpagote/infracost-terraform-jenkins-poc.git --branch=master --single-branch /tmp/base;

            echo "=== print folder contents ===";
            cd /tmp/base;
            ls -al;

            echo "=== infracost cost breakdown for current tf configuration ===";
            infracost breakdown --path=.;
        """)
    }
}
