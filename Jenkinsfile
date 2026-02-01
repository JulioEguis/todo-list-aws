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
            }
        }
    }
}
