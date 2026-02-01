pipeline {
    agent any
    stages {
        stage('Get Code') {
          steps {
              git branch: 'develop', url: 'https://github.com/JulioEguis/todo-list-aws.git'
            }
        }
   stage('Static Test') {
    steps {
        sh '''
        export PATH=/var/lib/jenkins/.local/bin:$PATH
        flake8 --exit-zero --format=pylint src > flake8.out
        bandit --exit-zero -r src -f custom -o bandit.out --msg-template "{abspath}:{line}: [{test_id}] {msg}"
        '''
                recordIssues tools: [flake8(name: 'Flake8', pattern: 'flake8.out')]
        
                recordIssues tools: [pyLint(name: 'Bandit', pattern: 'bandit.out')]
    }
}
               stage('Deploy') {
                steps {
                    sh '''
                        sam validate --region us-east-1
                        sam build
                        sam deploy --config-file samconfig.toml --config-env staging
                    '''
       stage('Rest Test') {
            steps {
                script {
                def BASE_URL = sh(script: "aws cloudformation describe-stacks --stack-name staging-todo-list-aws --query 'Stacks[0].Outputs[?OutputKey==\`BaseUrlApi\`].OutputValue' --region us-east-1 --output text", returnStdout: true).trim()
                withEnv(["BASE_URL=${BASE_URL}"]) {
            sh '''
                python3 -m pytest --junitxml=junit-report.xml test/integration/todoApiTest.py -v
            '''
            }
        }
        junit 'junit-report.xml'
    }
}

    stage('Promote') {
    steps {
        withCredentials([gitUsernamePassword(credentialsId: 'github-credentials', gitToolName: 'Default')]) {
            sh '''
                git config user.email "jenkins@jenkins.com"
                git config user.name "Jenkins CI"
                git checkout master
                git merge develop --no-edit
                git push origin master
            '''
        }
    }
}
            stage('Promote') {
            steps {
                withCredentials ([gitUsernamePassword(credentialsId: 'github-credentials', gitToolName: 'Default')]) {
                sh '''
                    git checkout master
                    
                '''
                }
            }
    
