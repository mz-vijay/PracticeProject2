#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG="vijay@mindzcloud.com"
    def SFDC_HOST = "https://login.salesforce.com"
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY="3MVG9n_HvETGhr3CCPqAGXhmZ4DO9hsPRbpn03GsQmwsrqjSfERZI5TQn2Gg1GbFmuso38GJuVUTf20DCssDu"

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStatus: true, script: "sfdx force:source:checkonly --checkonly --manifest manifest/package.xml -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStatus: true, script: "sfdx force:source:checkonly --checkonly --manifest manifest/package.xml -u ${HUB_ORG}"
			}
            if(rc != 0) { error 'Validate Failed' }

           /* if (isUnix()) {
				rmsg = sh returnStatus: true, script: "sfdx force:source:deploy  --manifest manifest/package.xml -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStatus: true, script: "sfdx force:source:deploy  --manifest manifest/package.xml -u ${HUB_ORG}"
			}

			println(rmsg)*/

            println('Hello from a Job DSL script!')
            println('rmsg='+rmsg)

        }
    }
}