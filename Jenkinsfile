@Library('intelligent-orchestration')
    import com.synopsys.*
    new com.demo.pipeline.EntryPoint().execute('io-manifest.yml')

pipeline {
    agent any

    environment {
      IO_POC_PROJECT_NAME = 'IO-POC-insecure-bank'
      IO_POC_PROJECT_VERSION = "1.0"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -e clean package -DskipTests'
            }
        }
 	//stage('Polaris') {
        //    steps {
        //        polaris arguments: 'analyze -w', polarisCli: 'Polaris'
	//    }
        //}
        stage('Clean Workspace') {
            steps {
                cleanWs()
	    }
        }
    }
}
