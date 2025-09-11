pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AKIASVHN6Z3X6XNM5GPB')   // Jenkins credentials ID
        AWS_SECRET_ACCESS_KEY = credentials('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')
        AWS_DEFAULT_REGION    = 'us-east-1' // change if needed
        S3_BUCKET             = 'packet-and-cloud'       // replace with your bucket
        CLOUDFRONT_ID         = 'E170O64106LZZT'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/syam-ch/syamo326.git'
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
                sh '''
                    aws s3 sync build/ s3://$S3_BUCKET --delete
                '''
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {
                sh '''
                    aws cloudfront create-invalidation \
                      --distribution-id $CLOUDFRONT_ID \
                      --paths "/*"
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ Build failed. Please check logs."
        }
        success {
            echo "✅ Frontend successfully deployed to S3 and CloudFront cache invalidated."
        }
    }
}
