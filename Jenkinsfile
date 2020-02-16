// Jenkinsfile
try 
{
  stage('checkout') {
    node {
      cleanWs()
      checkout scm
    }
  }

  // Run terraform init
  stage('init') {
    node {
        withCredentials([azureServicePrincipal(credentialsId: 'Azurecredential',
                                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                                    clientIdVariable: 'ARM_CLIENT_ID',
                                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                                    tenantIdVariable: 'ARM_TENANT_ID')])
       {
        ansiColor('xterm') {
          // sh 'terraform init'
          sh '/var/jenkins_home/terraform init'
        }
      }

    }
  }


  // Run terraform plan
  stage('plan') {
    node {
        withCredentials([azureServicePrincipal(credentialsId: 'Azurecredential',
                                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                                    clientIdVariable: 'ARM_CLIENT_ID',
                                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                                    tenantIdVariable: 'ARM_TENANT_ID')])      
      {
        ansiColor('xterm') {
          sh '/var/jenkins_home/terraform plan'
        }
      }
    }
  }

  if (env.BRANCH_NAME == 'master') {

    // Run terraform apply
    stage('apply') {
      node {
        withCredentials([azureServicePrincipal(credentialsId: 'Azurecredential',
                                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                                    clientIdVariable: 'ARM_CLIENT_ID',
                                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                                    tenantIdVariable: 'ARM_TENANT_ID')])        
        {
          ansiColor('xterm') {
            sh '/var/jenkins_home/terraform apply -auto-approve'
          }
        }
      }
    }

    // Run terraform show
    stage('show') {
      node {
        withCredentials([azureServicePrincipal(credentialsId: 'Azurecredential',
                                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                                    clientIdVariable: 'ARM_CLIENT_ID',
                                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                                    tenantIdVariable: 'ARM_TENANT_ID')])        
        {
          ansiColor('xterm') {
            sh '/var/jenkins_home/terraform show'
          }
        }
      }
    }
  }
  currentBuild.result = 'SUCCESS'

}
catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
  sh '/var/jenkins_home/terraform destroy -auto-approve'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  sh '/var/jenkins_home/terraform destroy -auto-approve'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}