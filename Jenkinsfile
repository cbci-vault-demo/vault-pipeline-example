// define the secrets and the env variables
// engine version can be defined on secret, job, folder or global.
// the default is engine version 2 unless otherwise specified globally.
def secrets = [[path: 'secret/jenkins/database/config', engineVersion: 2, secretValues: [[envVar: 'name', vaultKey: 'username']]]]

def secretValue

pipeline {
    agent any
    options { skipDefaultCheckout() }
    stages {
        stage("Get Secrets") {
            steps {
              // inside this block your credentials will be available as env variables
              withVault([vaultSecrets: secrets]) {
                script { secretValue = "$name" }
                sh 'echo $name'
              }
    
              echo "vault secretValue: $secretValue"
            }
        }
        stage("View Secrets?") {
            agent none
            steps {
              echo "vault secretValue: $secretValue"
            }
        }
        stage("Vault K8s Injector") {
            agent {
                kubernetes {
                  label 'vault-sidecar-injector'
                  yaml """
kind: Pod
metadata:
  name: vault-sidecar-injector
  namespace: cloudbees-core
  annotations:
    vault.hashicorp.com/agent-inject: "true"
    vault.hashicorp.com/agent-pre-populate: "true"
    vault.hashicorp.com/role: "jenkins-agent-role"
    vault.hashicorp.com/agent-inject-secret-jenkins-test: "secret/data/jenkins/test"
    vault.hashicorp.com/agent-inject-template-jenkins-test: |
      {{- with secret "secret/data/jenkins/test" -}}
        export TEST_NAME="{{ .Data.data.name }}"
      {{- end -}}
    vault.hashicorp.com/tls-skip-verify: "true"
spec:
  serviceAccountName: jenkins
                  """
                }
            }
            steps {
                echo "$TEST_NAME"
            }
        }
    }
}
