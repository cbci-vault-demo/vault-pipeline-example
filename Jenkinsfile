// define the secrets and the env variables
// engine version can be defined on secret, job, folder or global.
// the default is engine version 2 unless otherwise specified globally.
def secrets = [[path: 'secret/jenkins/database/config', engineVersion: 2, secretValues: [[envVar: 'name', vaultKey: 'username']]]]

def secretValue

pipeline {
    agent any
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
        stage('View Secrets?') {
            agent none
            steps {
              echo "vault secretValue: $secretValue"
            }
        }
    }
}
