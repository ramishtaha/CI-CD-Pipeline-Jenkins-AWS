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
                        trivy image --exit-code 1 --severity HIGH,CRITICAL nginx:latest
                    '''
              }
         }         
         stage('Upload to AWS') {
              steps {
                  withAWS(region:'ap-south-1',credentials:'jenkins') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'ramish-jenkins-multistep-pipeline')
                  }
              }
         }
         stage('Upload to AWS') {
              steps {
                  {
                      sh 'aws s3 cp s3://ramish-jenkins-multistep-pipeline/index.html ./www'
                      sh 'docker run --name tmp-nginx-container -d -p 80:80 nginx'
                      sh 'docker cp tmp-nginx-container:/etc/nginx/nginx.conf ./www'
                      
                  }
              }
         }
     }
}
