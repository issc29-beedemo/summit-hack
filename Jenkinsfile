node('jdk8') {

	stage 'build'
		env.PATH="${tool 'maven-3.3.9'}/bin:${env.PATH}"
		checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/issc29/workflow-demo']]])
		sh 'mvn clean package'
		archive 'target/*.war'

	stage 'integration-test'
		sh 'mvn verify'
		step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml', healthScaleFactor: 1.0])
}

stage 'quality-and-functional-test'

	parallel(qualityTest: {
    	node('jdk8') {
    		echo 'sonar scan'
        	// sh 'mvn sonar:sonar'
    	}
    }, functionalTest: {
    	echo 'selenium test'
        // build 'sauce-labs-test'
    })

    try {
        checkpoint('Testing Complete')
    } catch (NoSuchMethodError _) {
        echo 'Checkpoint feature available in Jenkins Enterprise by CloudBees.'
    }


stage 'approval'
	input 'Do you approve deployment to production?'
	echo 'modified'

stage 'production'
	echo 'mvn cargo:deploy'
