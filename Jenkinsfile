pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 sh 'echo "Hello World"'
                 sh '''
                     echo "Multiline shell steps works too"
                     ls -lah
                 '''
             }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Security Scan') {
              steps { 
                //  aquaMicroscanner imageName: 'alpine:latest', notCompleted: 'exit 1', onDisallowed: 'fail'
                    sh '''
                        trivy image alpine:latest
                        my_exit_code=$?
                        if [ ${my_exit_code} == 1 ]; then
                            echo "Image scanning failed. Some vulnerabilities found"
                            exit 1;
                        else
                            echo "Image is scanned Successfully. No vulnerabilities found"
                        fi;
                    '''
              }
         }         
         stage('Upload to AWS') {
              steps {
                  withAWS(region:'ap-south-1',credentials:'jenkins') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'ci-cd-ramish')
                  }
              }
         }
     }
}
