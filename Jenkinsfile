#!groovy

/**
 * This Jenkinsfile is intended to run on https://15.146.44.13 and may fail anywhere else.
 * It makes assumptions about plugins being installed, labels mapping to nodes that can build what is needed, etc.
 *
 * This Jenkinsfile incorporates 'Git Flow' workflow for Continuous Integration...
 *
 * @author vinay.makam-anjaneya@hpe.com
 * @date 25-03-2019
 * @version Initial Commit
 * @copyright HPE Confidential
 *
 * Setup:
 * - Configure the environment variables accordingly
 */

// Declarative pipeline must be enclosed within a pipeline block
pipeline {

    // agent section specifies where the entire Pipeline will execute in the Jenkins environment
    agent any

    options {
        buildDiscarder(
                // Only keep the 10 most recent builds
                logRotator(numToKeepStr: '5'))
    }

    environment {
        REPO_URL = 'git@github.hpe.com:vinay-makam-anjaneya/calculatorlibrary.git'
        // Your GitHub Repository
        DEVELOP_BRANCH = 'develop' // Your Development Branch
        PROJECT_NAME = 'CalculatorLibrary'
        COVERITY_HOST = 'localhost'
        COVERITY_PORT = '9090'
        COVERITY_STREAM = 'fast-data'
        COVERITY_USER = 'user'
        COVERITY_PASS = 'x'

        EMAIL_TO = 'vinay.makam-anjaneya@hpe.com'
        EMAIL_FROM = 'vinay.makam-anjaneya@hpe.com'
        VIRTUAL_ENV = "${env.WORKSPACE}/venv"
    }

    /**
     * the stage directive should contain a steps section, an optional agent section, or other stage-specific directives
     * all of the real work done by a Pipeline will be wrapped in one or more stage directives
     */

    stages {

        stage('Preparation') {
            steps {
                sh """
                    echo ${SHELL}
                    [ -d venv ] && rm -rf venv
                    python3 -m venv venv
                    export PATH=${VIRTUAL_ENV}/bin:${PATH}
                    pip install --upgrade pip
                    pip install -r requirements.txt
                """
            }
        }

/*        stage('Lint source') {
            steps {
                sh """
                   [ -d report ] || mkdir report
                   export PATH=${VIRTUAL_ENV}/bin:${PATH}
                   flake8 --exclude=venv* --statistics --ignore=E305, E112, E999
                """
            }
        }*/

/*        stage('Unit tests') {
            steps {
                sh """
                   export PATH=${VIRTUAL_ENV}/bin:${PATH}
                   pytest --cov=src --verbose --html=report.html --self-contained-html
                   pytest ./src --alluredir=report/allure-results
		 """
            }
        }*/
/*        stage('reports') {
            steps {
                script {
                    allure([
                            includeProperties: false,
                            jdk              : '',
                            properties       : [],
                            reportBuildPolicy: 'ALWAYS',
                            results          : [[path: 'report/allure-report']]
                    ])
                }
            }
        }*/

        stage('Coverity') {
            steps {
                withCoverityEnv('Cov-Analysis') {
		    sh "echo 'Coverity Analysis tool'"
		    sh "echo 'configuring coverity analysis compilers before run...'"
		    sh "cov-configure --python"
			
	            sh "echo 'building project using cov-build ...'"
                    sh "cov-build --dir idir --fs-capture-search ${WORKSPACE}/src --no-command"
	            
		    sh "echo 'analyzing project code using cov-analyze ...'"
                    sh "cov-analyze --dir idir --all"
		    sh "echo 'completed analyzing project code ...'"
			
		    sh "echo 'publishing coverity analysis report ...'"
		    sh "cov-format-errors --dir idir --html-output ${WORKSPACE}/coverity"
                    sh "cov-commit-defects --dir idir --host ${COVERITY_HOST} --port ${COVERITY_PORT} --stream ${COVERITY_STREAM} --user ${COVERITY_USER} --password ${COVERITY_USER}"
                }
            }
        }
    }
}
