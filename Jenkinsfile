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
        http_proxy=http://web-proxy.in.hpecorp.net:8080
        https_proxy=https://web-proxy.in.hpecorp.net:8080
        DEVELOP_BRANCH = 'develop' // Your Development Branch
        PROJECT_NAME = 'CalculatorLibrary'
        COVERITY_HOST = '15.146.44.13'
        COVERITY_PORT = '8085'
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
    }
}
