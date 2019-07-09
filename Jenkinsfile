#!groovy

node {
	
    def SF_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH // old val SF_CONSUMER_KEY
    def SF_USERNAME=env.HUB_ORG_DH // old val SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.JWT_CRED_ID_DH // old val SERVER_KEY_CREDENTALS_ID
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'
	
	// All possible SF test levels
	// NoTestRun
	// RunSpecifiedTests
	// RunLocalTests
	// RunAllTestsInOrg
	
	def SF_API_CONSUMER_KEY=credentials('id of credentils')
	def SF_USER_LOGIN_PASS=credentials('id of credentils')

    def toolbelt = tool 'toolbelt'


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------

    withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {
        // -------------------------------------------------------------------------
        // Authenticate to Salesforce using the server key.
        // -------------------------------------------------------------------------

        stage('Authorize to Salesforce') {
		
		
		// --------------------------------
		input message: 'Do you want to approve the deploy in production?', ok: 'Yes'
		// --------------------------------
		
		
            
			if (isUnix()) {
				rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl https://test.salesforce.com --clientid ${SF_CONSUMER_KEY} --jwtkeyfile ${server_key_file} --username ${SF_USERNAME} --setalias UAT"
			}
			else {
				rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl https://test.salesforce.com --clientid ${SF_CONSUMER_KEY} --jwtkeyfile \"${server_key_file}\" --username ${SF_USERNAME} --setalias UAT"
			}  
			
			if (rc != 0) {
                error 'Salesforce org authorization failed.'
            }
        }


        // -------------------------------------------------------------------------
        // Deploy metadata and execute unit tests.
        // -------------------------------------------------------------------------

        stage('Deploy and Run Tests') {
            
			rc = command "${toolbelt}/sfdx force:mdapi:deploy --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
            
			if (rc != 0) {
                error 'Salesforce deploy and test run failed.'
            }
        }


        // -------------------------------------------------------------------------
        // Example shows how to run a check-only deploy.
        // -------------------------------------------------------------------------

        //stage('Check Only Deploy') {
        //    rc = command "${toolbelt}/sfdx force:mdapi:deploy --checkonly --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
        //    if (rc != 0) {
        //        error 'Salesforce deploy failed.'
        //    }
        //}
    }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
		return bat(returnStatus: true, script: script);
    }
}
