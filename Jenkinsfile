pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '65915a71-0e6c-4256-831d-c0017c2cfec8'
        NETLIFY_AUTH_TOKEN = credentials('netlifyToken')  // ใช้ ID ที่ตั้งไว้
    }


    stages {
        stage('Install dependencies') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "📦 Installing dependencies..."
                sh '''
                    npm install
                '''
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "🔨 Building the project..."
                sh '''
                    npm run build
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

        stage('Deploy to Netlify') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "🚀 Deploying to Netlify..."
                sh '''
                    npm install netlify-cli
                    npx netlify link --id="$NETLIFY_SITE_ID"
                    npx netlify deploy \
                      --auth="$NETLIFY_AUTH_TOKEN" \
                      --site="$NETLIFY_SITE_ID" \
                      --dir=build \
                      --prod
                '''
            }
        }

        stage('Post Deploy') {
            steps {
                echo "🎉 Deployment complete! Check your site on Netlify."
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD pipeline executed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check logs."
        }
    }
}
