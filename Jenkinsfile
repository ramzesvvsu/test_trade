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
        stage('Update test database'){
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
                    cmd("deployka session lock -ras ${env.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka session kill -ras ${env.Server1C} -db ${env.Database1C} ${lockParams}")
                    cmd("deployka loadrepo /s${connectionString} \"${env.StoragePath}\" -storage-user ${env.Storage_Usr} -storage-pwd ${env.Storage_Psw} -uccode ${uccode} -v8version 8.3.10 -storage-ver ${versionValue}")
                    cmd("deployka dbupdate /s${connectionString} -allow-warnings -uccode ${uccode} -v8version 8.3.10")
                    cmd("deployka session unlock -ras ${env.Server1C} -db ${env.Database1C}")
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
        stage('Dim Tests')
        {
            
            steps{
                timestamps {
                    //cmd("vrunner xunit ./Tools/xUnitFor1C/Tests/Smoke --pathxunit ./Tools/xUnitFor1C/xddTestRunner.epf --reportxunit \"ГенераторОтчетаJUnitXML{out/junit.xml}\" --xddExitCodePath ./out/junitstatus.log --ibname /s${connectionString} --xddConfig ./tools/xUnitParams.json")
                    cmd("vrunner xunit ./Tools/xUnitFor1C/Tests/Smoke --pathxunit ./Tools/xUnitFor1C/xddTestRunner.epf --reportsxunit \"GenerateReportJUnitXML{out/junit.xml}\" --xddExitCodePath ./out/junitstatus.log --ibname /s${connectionString} --xddConfig ./tools/xUnitParams.json")
                }

            }
        }
        stage('Public result')
        {
            steps{
                script{
                    def allurePath = tool name: 'Allure 1.5.2', type: 'allure'
                    cmd("${allurePath}/bin/allure generate -o out/publishHTML/allure-report out/allure")

                }
                junit allowEmptyResults: true, testResults: 'out/junit.xml'

                cmd("pickles -f features -o out/publishHTML/pickles -l ru --df dhtml --sn \"Trade\"")
                step(
                    [$class: 'CukedoctorPublisher', 
                    featuresDir: 'out/cucumber', 
                    format: 'HTML', 
                    hideFeaturesSection: false, 
                    hideScenarioKeyword: false, 
                    hideStepTime: false, 
                    hideSummary: false, 
                    hideTags: false, 
                    numbered: true, 
                    sectAnchors: true, 
                    title: 'Living Documentation', 
                    toc: 'RIGHT']

                )
                publishHTML target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false, 
                    keepAll: false, 
                    reportDir: 'out/publishHTML', 
                    reportFiles: 'allure-report/index.html, pickles/Index.html', 
                    reportName: 'HTML Report', 
                    reportTitles: ''
                ]
                step(
                    [
                        $class: 'CucumberReportPublisher',
                        fileIncludePattern: '*.json',
                        jsonReportDirectory: 'out/cucumber'
                ]
                )
            }
        }
        stage('Prepare distr')
        {
            steps{
                timestamps{
                    cmd("packman load-storage \"${env.StoragePath}\" -use-tool1cd -storage-v ${versionValue}")
                    cmd("packman make-cf")
                    cmd("packman make-dist ./tools/package.edf -setup")
                    cmd("packman zip-dist -out out -name-prefix course_relise")

                    archiveArtifacts artifacts: 'out/*.zip', onlyIfSuccessful: true

                }
            }
        }
    }
}

def cmd(command) {
    if (isUnix()){
        sh "${command}"
    } else {
        bat "chcp 1251\n${command}"
       // bat "chcp 65001\n${command}"
    }    
}
