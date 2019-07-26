pipeline {
    agent any
    stages{
        stage ('Code Checkout') {
            steps {
                checkout ([$class: 'GitSCM', 
                branches: [[name: '*/master']], 
                userRemoteConfigs: [[url: 'https://github.com/somasekharkongurutu/addressbook.git']] ])
            }
        }
        stage ('Compiling Code') {
            steps {
                withMaven {
                    sh 'mvn clean compile'
                }
            }
        }
        stage ('PMD Analysis') {
            steps {
                withMaven {
                    sh 'mvn -P metrics pmd:pmd'
                }
            }
            post {
                always {
                    pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '**/target/pmd.xml', unHealthy: ''
                }
            }
        }
        stage ('Unit Testing') {
            steps {
                withMaven {
                    sh 'mvn test'
                }
            }
        }
        stage ('Code Metrics') {
            steps {
                withMaven {
                    sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
                }
            }
            post {
                always {
                    cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
                }
            }
        }
        stage ('Package') {
            steps {
                withMaven {
                    sh 'mvn package'
                }
            }
            post {
                always {
                    sh 'scp target/addressbook.war viswa@zeta-tomcat1:/var/lib/tomcat8/webapps'
                }
            }
        }
    }
    
}
