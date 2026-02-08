pipeline {
    agent any
    stages {
        stage('Get Code') {
          steps {
              git branch: 'master', url: 'https://github.com/JulioEguis/todo-list-aws.git'
            }
        }
               stage('Deploy') {
                steps {
                    sh '''
                        sam validate --region us-east-1
                        sam build
                        sam deploy --config-file samconfig.toml --config-env production --no-fail-on-empty-changeset
                    '''
         }
     }
       stage('Rest Test') {
            steps {
                script {
                def BASE_URL = sh(script: "aws cloudformation describe-stacks --stack-name staging-todo-list-aws --query 'Stacks[0].Outputs[?OutputKey==`BaseUrlApi`].OutputValue' --region us-east-1 --output text", returnStdout: true).trim()
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
                git fetch origin
                git checkout master
                git reset --hard origin/master
                git merge -X ours develop --no-edit 
                git push origin master
                
            '''
        }
    }
}
}
}
            
