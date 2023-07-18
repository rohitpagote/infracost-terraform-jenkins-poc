pipeline {
    agent any
    stages {
        stage('infracost API key'){
              steps {
                  withCredentials([
                    usernamePassword(
                        credentialsId: '99b628db-41fb-4ee2-b781-65f90fd8f9b8', 
                        usernameVariable: 'USER', 
                        passwordVariable: 'PASS'
                        )]) {
                    sh '''
                        echo "The username is: ${USER}"
                        echo "The password is : ${PASS}"
                    '''
                }
              }
        }
        stage('infracost') {

            // Set up any required credentials for posting the comment, e.g. GitHub token, GitLab token
            environment {
                // INFRACOST_API_KEY = credentials('jenkins-infracost-api-key')
                // no_proxy = 'pricing.api.infracost.io'
                INFRACOST_API_KEY = "${PASS}"
                // The following environment variables are required to show Jenkins PRs on Infracost Cloud.
                //  These are the minimum required, and you should alter to conform to your specific setup.
                //  To read more about additional environment variables you can use to customize Infracost Cloud,
                //  visit here: https://www.infracost.io/docs/features/environment_variables/#environment-variables-to-set-metadata
                INFRACOST_VCS_PROVIDER = 'github'
                INFRACOST_VCS_REPOSITORY_URL = 'https://github.com/rohitpagote/infracost-terraform-jenkins-poc.git'
                INFRACOST_VCS_PULL_REQUEST_URL = 'https://github.com/rohitpagote/infracost-terraform-jenkins-poc/pull/3'
                INFRACOST_VCS_PULL_REQUEST_AUTHOR = 'Rohit Pagote'
                INFRACOST_VCS_PULL_REQUEST_TITLE = 'Change instance type'
                INFRACOST_VCS_BRANCH = 'master'
                INFRACOST_VCS_BASE_BRANCH = 'master'
                INFRACOST_VCS_COMMIT_SHA = '3ab6d626f5172b205c1a068dbaf346b23bf94e20'
                // If you're using Terraform Cloud/Enterprise and have variables or private modules stored
                // on there, specify the following to automatically retrieve the variables:
                // INFRACOST_TERRAFORM_CLOUD_TOKEN: credentials('jenkins-infracost-tfc-token')
                // Change this if you're using Terraform Enterprise
                // INFRACOST_TERRAFORM_CLOUD_HOST: app.terraform.io
                // INFRACOST_TLS_INSECURE_SKIP_VERIFY = true
                INFRACOST_TLS_CA_CERT_FILE='/etc/ssl/certs/ca-certificates.crt'
            }

            steps {
                // Get the infracost version
                sh 'infracost --version'
                // sh 'curl -i https://pricing.api.infracost.io/health -H X-Api-Key: ico-OJbEftyjeWTWOqSBTUavTPNIlbPVHjz5 -insecure'
                // Clone the base branch of the pull request (e.g. main/master) into a temp directory.
                sh 'git clone https://github.com/rohitpagote/infracost-terraform-jenkins-poc.git --branch=master --single-branch /tmp/base'

                sh 'cd /tmp/base'
                //sh 'terraform init -lockfile=readonly'
                sh 'ls'

                // sh 'curl -vvv https://pricing.api.infracost.io/ --cacert cacert.pem -k'

                sh 'infracost breakdown --path=.'

                // Generate Infracost JSON file as the baseline, add any required sub-directories to path, e.g. `/tmp/base/PATH/TO/TERRAFORM/CODE`.
                sh 'infracost breakdown --path=. \
                                        --format=json \
                                        --out-file=infracost-base.json'

                // Generate an Infracost diff and save it to a JSON file.
                sh 'infracost diff --path=/tmp/base \
                                   --format=json \
                                   --compare-to=infracost-base.json \
                                   --out-file=infracost.json'

                // Posts a comment to the PR using the 'update' behavior.
                // This creates a single comment and updates it. The "quietest" option.
                // The other valid behaviors are:
                //   delete-and-new - Delete previous comments and create a new one.
                //   hide-and-new - Minimize previous comments and create a new one.
                //   new - Create a new cost estimate comment on every push.
                // See https://www.infracost.io/docs/features/cli_commands/#comment-on-pull-requests for other options.
                // sh 'infracost comment github --path=/tmp/infracost.json \
                //                              --repo=https://github.com/rohitpagote/infracost-terraform-jenkins-poc.git \
                //                              --pull-request=$GITHUB_PULL_REQUEST_NUMBER \
                //                              --github-token=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 \
                //                              --behavior=update'
            }
        }
    }
}
