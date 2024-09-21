## Pipeline Scripts Example

---

### 1. **Pipeline with all the stages( using echo statements only )**

This pipeline doesn't execute any real build, test, or deploy actions, but it helps you validate the pipeline structure and flow

pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests after build?')
        choice(name: 'DEPLOY_ENV', choices: ['dev', 'staging', 'production'], description: 'Deployment environment')
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out branch: ${params.BRANCH_NAME} from GitHub..."
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
            }
        }

        stage('Run Shell Script') {
            steps {
                echo 'Running custom shell script...'
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo 'Running tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying the application to ${params.DEPLOY_ENV} environment..."
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }

        always {
            echo 'Cleaning up the workspace...'
        }
    }
}
...

---


### 2. **Pipeline with Shell Scripts**

This pipeline uses shell scripts to run build and test commands.

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/username/repository.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Running build script...'
                sh './build.sh'   // Executing a custom shell script
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh './test.sh'    // Executing test shell script
            }
        }
    }

    post {
        always {
            echo 'Clean up workspace after build'
            sh 'rm -rf target/*'   // Cleanup command
        }
    }
}
```

---

### 3. **Pipeline with Maven Jobs**

This pipeline uses Maven for the build and test phases.

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven 3.6.3'   // Specify Maven version from Jenkins tool configuration
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/username/repository.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building with Maven...'
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests with Maven...'
                sh 'mvn test'
            }
        }
    }

    post {
        success {
            echo 'Maven build and test completed successfully!'
        }
        failure {
            echo 'Maven build failed!'
        }
    }
}
```

---

### 4. **Pipeline with Post-Build Actions**

This pipeline includes post-build actions, such as sending notifications on success or failure.

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/username/repository.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
    }

    post {
        success {
            echo 'Build was successful!'
            // Example: Send a notification (e.g., to Slack or email)
            // slackSend(channel: '#build-notifications', message: 'Build Success!')
        }

        failure {
            echo 'Build failed!'
            // slackSend(channel: '#build-notifications', message: 'Build Failed!')
        }
    }
}
```

---

### 5. **Pipeline with GitHub SCM**

This pipeline clones a repository from GitHub using SSH authentication.

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:username/repository.git', credentialsId: 'github-ssh-credentials'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
    }
}
```

---

### 6. **Pipeline with Parameters**

This pipeline accepts parameters to customize the build (e.g., choosing the branch to build or deciding whether to run tests).

```groovy
pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests after build?')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${params.BRANCH_NAME}", url: 'https://github.com/username/repository.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
    }
}
```

---

### 7. **Pipeline Combining All Features**

This final pipeline example combines shell scripts, Maven jobs, post-build actions, GitHub SCM integration, and parameters.

```groovy
pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Branch to build')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests after build?')
    }

    tools {
        maven 'Maven 3.6.3'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out branch ${params.BRANCH_NAME}..."
                git branch: "${params.BRANCH_NAME}", url: 'git@github.com:username/repository.git', credentialsId: 'github-ssh-credentials'
            }
        }

        stage('Build') {
            steps {
                echo 'Building with Maven...'
                sh 'mvn clean install'
            }
        }

        stage('Run Shell Script') {
            steps {
                echo 'Running shell script...'
                sh './custom-script.sh'
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
            // Example: Send success notification
            // slackSend(channel: '#build-notifications', message: 'Build Success!')
        }

        failure {
            echo 'Pipeline failed!'
            // Example: Send failure notification
            // slackSend(channel: '#build-notifications', message: 'Build Failed!')
        }

        always {
            echo 'Cleaning up workspace...'
            sh 'rm -rf target/*'
        }
    }
}
```

---

### Explanation of the Final Pipeline:

- **Shell Scripts**: The `Run Shell Script` stage runs a custom shell script, `./custom-script.sh`.
- **Maven Jobs**: The `Build` and `Test` stages use Maven to build the project and run tests.
- **Post-Build Actions**: Notifications or cleanups are handled in the `post` section. Success or failure messages can be sent via tools like Slack.
- **GitHub SCM**: The `Checkout` stage pulls the code from GitHub using an SSH URL and `credentialsId`.
- **Parameters**: The pipeline accepts two parameters: `BRANCH_NAME` (to specify the branch) and `RUN_TESTS` (to conditionally run tests).
