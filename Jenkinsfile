node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        junit 'test-reports/results.xml'
    }
    stage('Manual Approval'){
        input message: 'Lanjut ke tahap Deploy? (Klik "Proceed untuk lanjutkan")'
    }
    stage('Deploy') {
        sh "docker run --rm -v \$(pwd)/sources:/src cdrx/pyinstaller-linux:python2 'pyinstaller -F add2vals.py'"
        
        archiveArtifacts 'sources/dist/add2vals'
        
        sshagent(['SSH_GCP_JENKINS']) {
            sh '''
            echo "Starting deployment to Cloud..."
            scp -o StrictHostKeyChecking=no sources/dist/add2vals fadlinarsin12@35.226.98.155:~/python-app/artifacts
            '''
        }

        // sleep 60s
        sleep 60
        echo 'Deploy success'
    }
}