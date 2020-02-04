//@Library('jenkins-cicd-shared-lib') _

pipeline {
    agent docker { image 'node:12-stretch' } 
    environment {
        NPM_TOKEN             = credentials('npmjs bcolferlp PAT')
    }

    stages {

	    stage('Checkout Source'){
			steps {
				checkout scm
			}
	    }

	    stage('Unit Test') {
			steps {
				withEnv(['NODE_ENV="test"','NVM_DIR="~/.nvm"']) {// Node specific term for unit tests
					echo "Node environment will be:  ${NODE_ENV}"
					echo "NPM_TOKEN is ${NPM_TOKEN}"
					sh label: 'node', script: '''
						nvm --version
						node -v
						npm prune
					'''
					sh label:'npm', script: '''
						npm ci
						npm test
					'''
				}
			}
	    }

	    stage('Deploy to test environments') {
			steps {
				withEnv(["NODE_ENV='integration'"]) {// Node specific term for unit tests
					sh ' echo "Node environment will be:  ${NODE_ENV}"'

					echo "Here is where the deploy to stage integration would happen"
					echo "serverless deploy --stage=${NODE_ENV} "
				}
			}
	    }

	    stage('Staging tests') {
			when {
				branch 'master'
			}
			failFast true

			parallel {
				stage('Run functional tests')  {
				steps { echo "Run functional tests" }
				}
				stage ('Run security tests')  {
				steps { echo "Run security tests" }
				}
			}

	    }

	    stage ('deploy to production') {
			input {
				message 'Deploy to production?'
				ok "Yes, the deployment has been verified."
				submitter "squad-leaders,executives,devops"
			}
			steps {
				echo "deploy to production"
				sh 'echo "#serverless deploy command:"'
				echo "serverless deploy --stage=production"
			}
	    }
	    stage('Production tests') {
			steps {
				echo "run the production tests here"
			}
	    }
    }
}
