pipeline {
    agent any
    parameters {
        string(name: 'Project', defaultValue: 'Demo Project', description: 'Project to Deploy')
        choice(name: 'Environment', choices: ['UAT', 'STAGE'], description: 'Select Workspace Environment')
        booleanParam (name : 'RUN_PLAN_ONLY', defaultValue: true, description: 'Use Checkbox checked to Run Terraform Plan Only. Uncheck Checkbox to Run Terraform Plan with Apply/Destroy Stage.')
        choice(name: 'TERRAFORM_ACTION', choices: ['apply' , 'destroy'],  description: 'Do You Want to Apply or Destroy?')
        string(name: 'Branch', defaultValue: 'master', description: 'Enter Branch Name to Run')
    }
    stages {
        stage('CleanWorkspace'){
           steps {
              cleanWs()
              sh 'whoami'
           }
        }
        stage('Terraform Code Pull'){
           steps {
              git branch: '${Branch}', url: 'https://github.com/phani-rudra9/terfraform-modules-latest.git'
           }
        }
        stage('Terraform Initialize'){
           steps {
              sh 'terraform init -var-file="${Environment}.tfvars"'
           }
        }
        stage('Select Workspace'){ 
           steps {
              sh 'terraform workspace new ${Environment} || terraform workspace select ${Environment}'
            }   
                
        }    
        stage('Terraform Plan'){ 
           steps {
              sh 'terraform plan -var-file="./${Environment}.tfvars" -out=${Environment}tfplanout'
            //   script {
            //   timeout(time: 10, unit: 'MINUTES') {
            //     input(id: "Terraform Apply Gate", message: "Terraform Plan ${params.Environment}tfplanout?", ok: 'Apply')
            //   }
            // }
		   }
        }
        stage('Terraform Apply') {
            when {
                expression { params.TERRAFORM_ACTION == 'apply' && params.RUN_PLAN_ONLY == false}
            }
            steps {
              sh 'terraform apply -auto-approve "${Environment}"tfplanout'
            }    
        }
        stage('Terraform Destroy') {
            when {
                expression { params.TERRAFORM_ACTION == 'destroy' && params.RUN_PLAN_ONLY == false}
            }
            steps {
              sh 'terraform destroy -auto-approve -var-file="./${Environment}.tfvars"'
            }    
        }
    }    
}
