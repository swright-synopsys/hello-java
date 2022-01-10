//@Library('intelligent-orchestration')
//    import com.synopsys.*
//    new com.demo.pipeline.EntryPoint().execute('io-manifest.yml')

pipeline {
    agent any

    environment {
      IO_URL = "http://io.synopsys.com/api/ioiq"
      POLARIS_ACCESS_TOKEN = credentials('polaris-token')
      IO_ACCESS_TOKEN = credentials('IO-AUTH-TOKEN')
      GITHUB_ACCESS_TOKEN = credentials('Github-AuthToken')

      //IO_POC_PROJECT_NAME = 'IO-POC-insecure-bank'
      //IO_POC_PROJECT_VERSION = "1.0"
    }

    stages {
    	stage('IO Prescription') {
              steps {
                echo "Getting IO Prescription"
                sh '''
                  rm -fr prescription.sh
                  wget "https://raw.githubusercontent.com/synopsys-sig/io-artifacts/${WORKFLOW_CLIENT_VERSION}/prescription.sh"
                  sed -i -e 's/\r$//' prescription.sh
                  chmod a+x prescription.sh
                  ./prescription.sh \
                  --stage="IO" \
                  --persona="developer" \
                  --io.url="${IO_URL}" \
                  --io.token="${IO_ACCESS_TOKEN}" \
                  --manifest.type="yml" \
                  --asset.id="swright-hello-java" \
                  --workflow.url="${WORKFLOW_URL}" \
                  --workflow.version="${WORKFLOW_CLIENT_VERSION}" \
                  --file.change.threshold="10" \
                  --sast.rescan.threshold="20" \
                  --sca.rescan.threshold="20" \
                  --scm.type="github" \
                  --scm.owner="swright-synopsys" \
                  --scm.repo.name="hello-java-io" \
                  --scm.branch.name="main" \
                  --github.username="swright-snopsys" \
                  --github.token="${GITHUB_ACCESS_TOKEN}" \
                  --polaris.project.name="swright-hello-java" \
                  --polaris.url="https://sipse.polaris.synopsys.com" \
                  --polaris.token="${POLARIS_ACCESS_TOKEN}" \
                  --jira.enable="false" \
                  --IS_SAST_ENABLED="false" \
                  --IS_SCA_ENABLED="false" \
                  --IS_DAST_ENABLED="false"
                  cat result.json | json_pp
                  IS_SAST_ENABLED=$(jq -r '.security.activities.sast.enabled' result.json)
                  echo "IS_SAST_ENABLED = ${IS_SAST_ENABLED}"
                '''
                //script {
                //    env.IS_SAST_ENABLED=${IS_SAST_ENABLED}
                //}
              }
        }
        stage('Build') {
            steps {
                sh 'mvn -e clean package -DskipTests'
            }
        }
 	stage('Polaris') {
            //when {
            //    expression { ${env.IS_SAST_ENABLED} == "true" }
            //}
            steps {
                polaris arguments: 'analyze -w', polarisCli: 'PolarisCLI'
	    }
        }
        stage('Clean Workspace') {
            steps {
                cleanWs()
	    }
        }
    }
}
