node {
    withCredentials([string(credentialsId: 'yZWTnwCb58z8ci5XXByJiTPI', variable: 'VERCEL_CREDENTIALS')]) {
    stage('Build') {
        try {
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
            input message: 'Lanjutkan ke tahap Deploy?'
        } catch (err) {
            echo "Pipeline aborted"
            
        }
    }

    stage('Deliver to ') {
        try {
	   // Use Docker to run Vercel CLI commands
            docker.image('vercel/vercel:latest').inside {
                // Deploy to Vercel
                sh 'vercel --prod --token ${VERCEL_CREDENTIALS}' // Use appropriate Vercel CLI commands and options for your deployment
            }
        } catch (Exception e) {
            echo "Deliver failed"
        }       
    }
  }
}


