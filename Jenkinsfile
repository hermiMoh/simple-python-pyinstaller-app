pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build'){
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test'){
            steps {
                sh 'py.test --junit-xml test-reports/result.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/result.xml'
                }
            }
        }
        stage ('Deliver'){
            agent {
                docker {
                    image 'python:3.9' // Use an official Python image with pip pre-installed
                    reuseNode true // Optional: helps with workspace sharing
                }
            }
            steps {
                // Now we are inside a fresh Python 3.9 container!
                sh 'pip install pyinstaller' // Install pyinstaller inside this temporary containe
                sh "pyinstaller --onefile sources/add2vals.py"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}