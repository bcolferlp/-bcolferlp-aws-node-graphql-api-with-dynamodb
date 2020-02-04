//@Library('jenkins-cicd-shared-lib') _

pipeline {
    agent { any }
    environment {
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
        NPM_TOKEN             = credentials('npmjs bcolferlp PAT')
    }

    stage('Checkout Source'){
        checkout scm
    }
    stage('Unit Test') {
        steps {
            withEnv(["NODE_ENV='test'"]) {// Node specific term for unit tests
                print "Node environment will be:  ${NODE_ENV}"
                print "NPM_TOKEN is ${NPM_TOKEN}"
                sh 'node -v'
                sh 'npm prune'
                sh 'npm ci'
                sh 'npm test'
            }
        }
    }

    stage('Deploy to test environments') {
        steps {
            withEnv(["NODE_ENV='integration'"]) {// Node specific term for unit tests
                print "Node environment will be:  ${NODE_ENV}"

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
                steps { echo "Run functional tests"}
            }
            stage ('Run security tests')  {
                steps { echo "Run security tests"}
            }
        }

    }

    stage ('production') {
        timeout(time:24, unit: 'HOURS') {
            input {
                message 'Deploy to production?'
                ok "Yes, the deployment has been verified."
                submitter "squad-leaders,executives,devops"
            }
        }
        steps {
            echo "deploy to production"
            sh 'echo "#serverless deploy command:"'
            echo "serverless deploy --stage=production"
        }
    }
    stage('Production tests') {
        echo "run the production tests here"
    }
}
