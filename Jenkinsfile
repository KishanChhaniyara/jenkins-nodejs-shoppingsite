pipeline {
    agent any

/*     parameters {
        booleanParam(
            name: 'RUN_TESTS',
            defaultValue: true,
            description: 'Run unit tests'
        )
    } */

    tools {
        nodejs 'nodejs-22-6-0'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        MONGO_URI = 'mongodb+srv://supercluster.d83jj.mongodb.net/superData'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }
/* 
        stage('Run Tests') {
            when {
                expression { params.RUN_TESTS }
            }
            steps {
                sh 'npm test'
            }
        } */

        stage('Build Project') {
            steps {
                sh 'npm run build || echo "No build script"'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
