pipeline {
 agent any

    	        // Environment Variables
	        environment {
	        MAJOR = '9'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
        	UIPATH_ORCH_LOGICAL_NAME = "suntechnology"
        	UIPATH_ORCH_TENANT_NAME = "RPA_Demo"
        	UIPATH_ORCH_FOLDER_NAME = "Shared"
	    }
	

	   stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }

	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
	                UiPathPack (
                      outputPath: "Output\\${env.BUILD_NUMBER}",
                      projectJsonPath: "project.json",
                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                      useOrchestrator: false,
					  traceLevel: 'None'
        )
	            }
	        }
	         
	

	         // Deploy Stages
	        stage('Deploy to Orchestrator') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
                UiPathDeploy (
                packagePath: "Output\\${env.BUILD_NUMBER}",
                orchestratorAddress: "${UIPATH_ORCH_URL}",
                orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                environments: 'DEV',
                //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
                credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
				traceLevel: 'None',
				entryPointPaths: 'Main.xaml'
	

	        )
	            }
	        }
	

	// Test Stages
	        stage('Execute Worflow') {
	            steps {
	               echo 'Testing the workflow...'
					UiPathTest (
					  testTarget: [$class: 'TestSetEntry', testSet: "PRMS_Sprint1_TC"],
					  orchestratorAddress: "${UIPATH_ORCH_URL}",
					  orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
					  folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					  timeout: 10000,
					  traceLevel: 'None',
					  testResultsOutputPath: "result.xml",
					  //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: "credentialsId"]
					  credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'),
					  parametersFilePath: ''
					)
	            }
			}
				
	       
	         	
		
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        
	    }
	
	}
