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
                        sam build
                        sam deploy --config-file samconfig.toml --config-env production --no-fail-on-empty-changeset
                    '''
         }
     }
       stage('Rest Test') {
            steps {
                script {
                def BASE_URL = sh(script: 'aws cloudformation describe-stacks --stack-name production-todo-list-aws --query "Stacks[0].Outputs[?OutputKey==\\`BaseUrlApi\\`].OutputValue" --region us-east-1 --output text', returnStdout: true).trim()
                withEnv(["BASE_URL=${BASE_URL}"]) {
            sh '''
                python3 -m pytest -k "listtodos or gettodo" --junitxml=junit-report.xml test/integration/todoApiTest.py 
            '''
            }
        }
        junit 'junit-report.xml'
    }
}
  
}
}

            
