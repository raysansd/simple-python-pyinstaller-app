node {
    stage('Build') {
        try {
	    sh 'pwd'
	    sh 'ls -la'
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        } catch (Exception e) {
            echo "Build failed"
        }
    }

    stage('Test') {
        try {
            docker.image('qnib/pytest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            junit 'test-reports/results.xml'
        } catch (Exception e) {
            echo "Test failed"
        }
    }

    stage('Manual Approval') {
        try {
            input message: 'Lanjutkan ke tahap Deploy ?'
        } catch (err) {
            echo "Pipeline aborted"
            
        }
    }

    stage('Deliver') {
        try {
            env.VOLUME = "${pwd()}/sources:/src"
            env.IMAGE = 'cdrx/pyinstaller-linux:python2'

            dir("${env.BUILD_ID}") {
                sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
            }
	    archiveArtifacts "sources/dist/add2vals"
	    sh "ls sources/dist"
	    sh "sources/dist/add2vals 10 10"
	    sh "sleep 60"
            sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
        } catch (Exception e) {
            echo "Deliver failed"
        }       
    }
}


