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
        stage('aws auth check') {
            steps {
                script {
                    withCredentials([[
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: "AW-WNeFBUIY",
                        accessKeyVariable: 'AWSAccessKeyId',
                        secretKeyVariable: 'AWSSecretKey'
                        ]]) {
                            sh( """
                                ls
                                echo "aws_access_key_id = $AWSAccessKeyId" >> credentials-aws
                                echo "aws_secret_access_key = $AWSSecretKey" >> credentials-aws
                                """
                            )
                        }
                }
            }
        }
        stage('google auth check'){
            steps{
                script{
                    withCredentials([file(credentialsId: 'GC-f2NYX6Ns', variable: 'GCPServiceAccount', typeVariable: 'type', projectIdVariable: 'project_id')]) {
                        // sh("gcloud auth activate-service-account --key-file=${GC_KEY}")
                        sh("cat ${GCPServiceAccount}")
                        sh( """
                            ls
                            echo "type = $type" >> credentials.json
                            echo "project_id = $project_id" >> credentials.json
                            ls
                            """
                        )
                    }
                }
            }
        }
        // stage('Infracost Version') {
        //     steps {
        //         script {
        //             try {
        //                 getInfracostVersion()
        //             } catch (exc) {
        //                 echo "Error occurred at infracost version stage}"
        //                 throw exc
        //             }
        //         }
        //     }
        // }

        // stage('Checkout') {
        //     steps {
        //         script {
        //             try {
        //                 doCheckout()
        //             } catch (exc) {
        //                 echo "Error occurred at checkout stage"
        //                 throw exc
        //             }
        //         }
        //     }
        // }

        // stage('Breakdown') {
        //     steps {
        //         script {
        //             try {
        //                 doBreakDown()
        //             } catch (exc) {
        //                 echo "Error occurred at breakdown stage"
        //                 throw exc
        //             }
        //         }
        //     }
        // }
    }
    // post {
    //     cleanup {
    //         echo '=== performing cleanup'
    //         deleteDir()
    //     }
    // }
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
    sh("""
        echo "=== removing tfvars file if any"
        rm -f common.auto.tfvars deployment.auto.tfvars provider.env

        echo "=== print folder contents"
        pwd
        ls -al
    """)
}

def doBreakDown() {
    script {
        sh("""
            pwd;

            echo "=== print folder contents ===";
            cd /var/jenkins_home/workspace/infracost-terraform-jenkins-poc-pipeline/google;
            ls -al;

            echo "=== infracost cost breakdown for current tf configuration ===";
            infracost breakdown --path=.;
        """)
    }
}
