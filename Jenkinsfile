def connectionString
def uccode
def lockParams

def versionText
def versionValue

pipeline{
    agent{
        label 'VanessaTest2'
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
                    //bat("oscript --version")
                    /*cmd("deployka session lock -ras ${env.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka session kill -ras ${env.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka loadrepo /s${connectionString} \"${env.StoragePath}\" -storage-user ${env.Storage_Usr} -storage-pwd ${env.Storage_Psw} -uccode ${uccode} -v8version 8.3.10 -storage-ver ${versionValue}")
                    cmd("deployka dbupdate /s${connectionString} -allow-warnings -uccode ${uccode} -v8version 8.3.10")
                    cmd("deployka session unlock -ras ${env.Server1C} -db ${env.Database1C}")*/
                 /**/  
                }
            }
        }
        stage('Test behavior')
        {
            
            steps{
                timestamps {
                    cmd("vrunner vanessa --pathvanessa ./Tools/vanessa-behavior/vanessa-behavior.epf --vanessasettings ./tools/vbParams.json --workspace . --ibname /s${connectionString}")
                }

            }
        }
        stage('Public result')
        {
            steps{
                script{
                    def allurePath = tool name: 'Allure 1.5.2', type: 'allure'
                    cmd("${allurePath}/bin/allure generate -o out/allure-report out/allure")

                }
                publishHTML target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false, 
                    keepAll: false, 
                    reportDir: 'out/allure-report', 
                    reportFiles: 'index.html', 
                    reportName: 'HTML Report', 
                    reportTitles: ''
                ]

            }
        }
    }
}

def cmd(command) {
    if (isUnix()){
        sh "${command}"
    } else {
        bat "chcp 1251\n${command}"
    }    
}
