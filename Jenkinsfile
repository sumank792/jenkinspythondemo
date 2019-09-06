pipeline {
    agent any
    stages {
	stage('Pull code'){
		steps{
			echo 'downloading git directory..'
			git 'https://github.com/pawnu/jenkinspythondemo.git'
		}
	}
	stage('Setup talisman if not present'){
            steps {
		    script{
			echo 'Checking talisman installed for jenkins user'
			def folder = new File('/var/jenkins_home/.talisman/bin/')
			if(!folder.exists()){
				sh'''
				mkdir -p ~/.talisman/bin
				PATH="$HOME/.talisman/bin/:${PATH}"
				TALISMAN_HOME="$HOME/.talisman/bin"
				curl --silent  https://raw.githubusercontent.com/thoughtworks/talisman/master/global_install_scripts/install.bash > /tmp/install_talisman.bash && /bin/bash /tmp/install_talisman.bash			       
				'''
			}
		    }
            }
	}
	stage('Check git history'){
		steps{
			echo 'running talisman to check project history for secrets'
			sh '~/.talisman/bin/talisman_linux_amd64 --scan'
		}
	}
	stage('Check dependency'){
		steps{
			echo 'running python safety check on requirements.txt file'
			sh 'safety check -r requirements.txt'
		}
	}
	stage('Install dependency'){
		steps{
			sh """
        		virtualenv --no-site-packages .
        		source bin/activate
        		pip install -r requirements.txt
        		deactivate
        		"""
		}
	}
        stage('Run python project') {
            steps {
                echo 'Running python script'
		sh """
		source bin/activate
		python hello.py
		deactivate
		"""
            }
        }
        stage('Test insecure-package') {
            steps {
                echo 'Testing insecure dependency'
		sh """
		source bin/activate
		pip install insecure-package
		safety check
		"""
            }
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
