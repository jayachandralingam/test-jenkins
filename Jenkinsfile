// Importing the shared library
@Library('shared-library') _

pipeline {
   agent any

// Installing the maven tool 
    tools { 
        maven 'maven-test-project' 
    }

// importing the yaml function from the shared library to parse the yaml file
   stages {
        stage('parsing yaml content') {
			steps {
				yaml("config.yml")	
		}				
	}

/* In the following 4 stages we are going to change directory (cd) 
   in every dir according to value parsed from the config.yml;
   Then we are going to execute the appropriate maven command 
   exported also from the config.yml
*/

        stage('build') {
			steps {
				sh """
					cd $env.buildProjectFolder	
					$env.buildCommand
				"""
		}				
	}

        stage('database') {
			steps {
				sh """
					cd $env.databaseProjectFolder
					$env.databaseCommand
				"""
		}				
	}

        stage('deploy') {
			steps {
				sh """
					cd $env.buildProjectFolder
					$env.deployCommand
				"""
		}				
	}

// test stage is going to run 3 parallel stages: Regression, performance, integration
        stage('test') {
			parallel {
				stage('Performance test'){
					steps {
						sh """
							cd $env.testFolder
							$env.performanceTestCommand
						"""
					}
				}
				
				stage('Regression test'){
					steps {
						sh """
							cd $env.testFolder
							$env.regressionTestCommand
						"""
					}
				}
				
				stage('Integration test'){
					steps {
						sh """
							cd $env.testFolder
							$env.integrationTestCommand
						"""
				}
			}
		}
	}
}


	post {
		always {
			cleanWs ()
			}	
		failure {
			mail to: "$env.notificationRecipient",
				 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
				 body: "Something is wrong with ${BUILD_URL}"
			}
		success {
			mail to: "$env.notificationRecipient",
				 subject: "Success Pipeline: ${currentBuild.fullDisplayName}",
				 body: "Build success !! Deployment is successful!! Jenkins job ${BUILD_URL} "
		}
	}
}
