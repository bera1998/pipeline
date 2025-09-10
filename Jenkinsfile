pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET          = 'packet-and-cloud'   // replace with actual bucket
        CLOUDFRONT_ID      = 'E170O64106LZZT'        // replace with your distribution ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/bera1998/pipeline.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'aws-credentials-id', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws s3 sync build/ s3://$S3_BUCKET --delete
                    '''
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {
                withAWS(credentials: 'aws-credentials-id', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws cloudfront create-invalidation \
                        --distribution-id $CLOUDFRONT_ID \
                        --paths "/*"
                    '''
                }
            }
        }
    }
}
