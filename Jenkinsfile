pipeline {
	    agent { label 'malli' }
	    stages {
	        stage('SourceCode') {
	            steps {
	                git branch: 'master', url: 'https://github.com/yadavallimallikharjua/python-sample-vscode-flask-tutorial.git'

	            }
	        }
	        stage('Dependencies') {
	            steps {
	                sh 'python3 -m pip install --upgrade pip setuptools wheel'
	            }
	        }
	        stage('Install requirements') {
	            steps {
	                sh 'pip install -r requirements.txt'
	            }
	        }
	        stage('Run tests') {
	            steps {
	                sh 'python3 -m pytest --verbose --junit-xml test-reports/results.xml'
	            }
	        }
	        stage ('archiveArtifacts & Test Results') {
	            steps {
	                archiveArtifacts artifacts: '__pycache__/test_test1.cpython-38-pytest-7.1.2.pyc',followSymlinks: false
                    junit 'test-reports/results.xml'
	            }
	        }
            stage('Sonarqube') {
				environment {
					scannerHome = tool 'SONARSCANNER'
                }
                steps {
                withSonarQubeEnv('SONAR_9.4.0') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            	}
        	}
              stage ('Artifactory Configuration') {
                steps {
                    rtPipResolver (
                        id: "python_build",
                        serverId: "JFROG_IN",
                        repo: "malli"
                    )
                }
            }
            stage ('pip install') {
               steps {
                   rtPipInstall (
                        resolverId: "python_build",
                        args: "-r requirements.txt"
                   )
               }
            }
             stage ('Build Packages') {
                steps {
                    sh "python3 setup.py sdist upload -r local"
                    sh " python3 setup.py bdist_wheel upload -r local"
                }
            }
            stage ('Publish build info') {
                steps {
                    rtPublishBuildInfo (
                        serverId: "JFROG_IN"
                    )
                }
            }
    	}
}
             

