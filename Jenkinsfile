pipeline{
    agent any

    // parameters{
    //     choice(name: 'ENVIRONMENT', choices: ['prod-eu', 'prod-na', 'prod-apac'], description: 'Select the deployment environment')
    //     booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests before building the project')
    // }
    
    tools{
         nodejs 'nodejs-22-6-0'

    }

    options{
        timestamp()
        disableConcurrentBuilds()
        
    }
    environment{
        MONGO_URI = 'mongodb+srv://supercluster.d83jj.mongodb.net/superData'
        MONGO_USERNAME = credentials('mongo-db-username')
        MONGO_PASSWORD = credentials('mongo-db-password')
        JENKINS_TOKEN = credentials('jenkins-token')
    }

    stages{
        stage('Install Dependencies'){
            steps{
                sh 'npm ci' 
            }
        }
        
        stage('Run Tests'){
            when{
                expression { return params.RUN_TESTS }
            }
            steps{
                sh 'npm test'
            }
        }

        stage('Build Project'){
            steps{
                sh 'npm run build'
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