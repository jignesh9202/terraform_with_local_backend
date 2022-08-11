pipeline {

    parameters {
        string(name: 'environment', defaultValue: 'terraform', description: 'Workspace/environment file to use for deployment')
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')

    }


     environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

   agent  any
        options {
                timestamps ()
                ansiColor('xterm')
            }
    stages {
        stage('checkout') {
            steps {
                 
                    git branch: "main" , credentialsId: 'd0aba152-18d4-48b4-a074-5a84fe621254', url: "https://github.com/jignesh9202/terraform_learn.git"

                    }
                }
            }

        stage('Plan') {
            steps {
                sh "pwd; terraform init -input=false"
                sh "pwd; terraform workspace new ${environment}"
                sh "pwd; terraform workspace select ${environment}"
                sh "pwd; terraform plan -input=false -out tfplan "
                sh "pwd; terraform show -no-color tfplan > tfplan.txt"
            }
        }
        stage('Approval') {
           when {
               not {
                   equals expected: true, actual: params.autoApprove
               }
           }

           steps {
               script {
                    def plan = readFile 'tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
               }
           }
       }

        stage('Apply') {
            steps {
                sh "cd terraform; terraform apply -input=false tfplan"
            }
        }
    }