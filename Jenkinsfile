//@Library('jenkins-cicd-shared-lib') _

pipeline {
    agent {
        docker { image 'node:12-stretch' }
    }
	environment {
		 HOME = '.'
	}

    stages {

	    stage('Checkout Source'){
			steps {
				checkout scm
				echo "GIT_BRANCH: $GIT_BRANCH"
				echo "GIT_COMMIT: $GIT_COMMIT"
			}
	    }

	    stage('Unit Test') {
			environment {
        		NPM_TOKEN   = credentials('npmjs bcolferlp PAT')
				NODE_ENV	= "test"
    		}
			// TODO: remove
			// this might be needed if the env var doesn't work 
			//echo "//registry.npmjs.org/:_authToken=${NPM_TOKEN}" > ./.npmrc
			steps {
				echo "Node environment will be:  ${NODE_ENV}"
				echo "NPM_TOKEN is ${NPM_TOKEN}"
				sh label: 'node and npm cleanup', script: '''
					node --version
					npm prune 
				'''
				sh label:'npm', script: '''
					npm ci
					echo "#npm test"
				'''
			}
	    }

	    stage('Deploy to test environments') {
			environment {
				NODE_ENV	= "integration"
			}
			steps {
				sh ' echo "Node environment will be:  ${NODE_ENV}"'
				echo "Here is where the deploy to stage integration would happen"
				echo "serverless deploy --stage=${NODE_ENV} "
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
