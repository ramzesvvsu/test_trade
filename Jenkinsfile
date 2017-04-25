def connectionString
def uccode
def lockParams

def versionText
def versionValue

pipeline{
    agent{
        label 'Test_Trade'
    }
    environment {
        Storage = credentials('Storage_trade_CiBot')
    }
    stages {
        stage('update test contur'){
            steps{
                timestamps {
                    script {
                        connectionString = "\"${env.Server1C}\\${env.Database1C}\""
                        uccode = "\"123\""
                        lockParams = "-lockmessage \"test\" -lockuccode ${uccode}"
                        versionText = readFile encodig: 'UTF-8', file: 'VERSION'
                        versionValue = (versionText =~ /<VERSION>(.*)<\/VERSION>/)[0][1]
                    }
                    bat("oscript --version")
                    /*cmd("deployka session lock -ras ${env.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka session kill -ras ${env.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka loadrepo /s${connectionString} \"${env.StoragePath}\" -storage-user ${env.Storage_Usr} -storage-pwd ${env.Storage_Psw} -uccode ${uccode} -v8version 8.3.10 -storage-ver ${versionValue}")
                    cmd("deployka dbupdate /s${connectionString} -allow-warnings -uccode ${uccode} -v8version 8.3.10")
                    cmd("deployka session unlock -ras ${env.Server1C} -db ${env.Database1C}")
                 */  
                }
            }
        }

    }
}

def cmd(command) {
    if (isUnix()){
        sh "${command}"
    } else {
        bat "chcp 65001\n${command}"
    }    
}
