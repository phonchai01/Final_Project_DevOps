pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'nfp_yJ8wobZLXsb1gEw8weG7snYsij7b8HX5b183'  // ใส่ ID ใหม่ตรงนี้
        NETLIFY_AUTH_TOKEN = credentials('netlifyToken')            // ใช้ token จาก Jenkins Credential
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "🔍 Verifying required files..."
                sh '''
                    test -f index.html || (echo "index.html is missing!" && exit 1)
                    test -f netlify/functions/random-menu.js || (echo "The random menu function is missing!" && exit 1)
                    echo "✅ All necessary files are in place!"
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "🧪 Running function load test..."
                sh '''
                    node -e "require('./netlify/functions/random-menu.js'); console.log('Function loaded successfully!')"
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "🚀 Deploying the project to Netlify..."
                sh '''
                    npm install netlify-cli
                    npx netlify deploy \
                      --auth="$NETLIFY_AUTH_TOKEN" \
                      --site="$NETLIFY_SITE_ID" \
                      --dir=. \
                      --prod
                '''
            }
        }

        stage('Post Deploy') {
            steps {
                echo "🎉 Deployment is complete! Your website is now live."
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD pipeline executed successfully!"
        }
        failure {
            echo "❌ An error occurred during the pipeline execution. Please check the logs!"
        }
    }
}
