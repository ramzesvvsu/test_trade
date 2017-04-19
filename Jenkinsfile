def connectionString
def uccode
def lockParams

def versionText
def versionValue

pipeline{
    agent{
        label 'win'
    }
    environment {
        Storage = credentials('Storage_trade_CiBot')
    }
    stages {
        stage('Обновление тетового контура'){
            steps{
                timestamps {
                    script {
                        connectionString = "\"/${env.Server1C}\\${env.Database1C}\""
                        uccode = "\"123\""
                        lockParams = "-lockmessage \"test\" -lockuccode ${uccode}"
                        versionText = readFile encodig: 'UTF-8', file: 'VERSION'
                        versionValue = (versionText =~ /<VERSION>(.*)<\/VERSION>/)[0][1]
                    }
                    cmd("oscript --version")
                    /*cmd("deployka session lock -ras ${end.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka session kill -ras ${end.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka loadrepo ${connectionString} \"${env.StoragePath}\" -storage-user ${env.Storage_Usr} -storage-pwd ${env.Storage_Psw} -uccpde ${uccode}")
                    cmd("deployka dbupdate ${connectionString} -allow-warnings -uccode ${uccode}")
                    cmd("deployka session unlock -ras ${end.Server1C} -db ${env.Database1C}")
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