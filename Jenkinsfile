pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AKIAYITXJQMCK6FG6RUO')   // Jenkins credentials ID
        AWS_SECRET_ACCESS_KEY = credentials('9nGE99K24SOj7UOEtRPtpLcixmKx3Nm54Y0xATJ0')
        AWS_DEFAULT_REGION    = 'ap-south-1' // change if needed
        S3_BUCKET             = 'samba99'       // replace with your bucket
        CLOUDFRONT_ID         = 'EE6573JMAOSPI'
'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/demo-anil/samba.git'
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
