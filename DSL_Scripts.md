## DSL Example Scripts

### 1. Project with description

   ```
   job('Description-Project') {
    description('My first job')
}

  ```
### 2. Scm Project

```

job('Git-Project') {
     description('My second job')
    scm {
        github('https://github.com/example/repo.git', 'master')
    }
}

```

### 3. Building a Steps Job

```
job('example-freestyle-job') {
    description('This is a sample Freestyle project created using DSL Plugin.')

    scm {
        git('https://github.com/example/repo.git', 'master')
    }

    triggers {
        scm('H/5 * * * *')  // Poll the SCM every 5 minutes
    }

    steps {
        shell('echo "Building project..."')
        shell('mvn clean install')  // Run Maven build
    }

    publishers {
        archiveArtifacts('**/target/*.jar')  // Archive the build artifacts
    }
}

```

### 4. Building a Parameterized job

```
job('parameterized-freestyle-job') {
    description('Freestyle project with parameters')

    parameters {
        stringParam('BRANCH', 'master', 'Branch to build')
        booleanParam('RUN_TESTS', true, 'Run tests after build')
    }

    scm {
        git('https://github.com/example/repo.git', '${BRANCH}')
    }

    triggers {
        scm('H/5 * * * *')  // Poll the SCM every 5 minutes
    }

    steps {
        shell('echo "Building branch ${BRANCH}"')
        shell('mvn clean install')
        
        conditionalsteps {
            condition {
                booleanCondition('${RUN_TESTS}')
            }
            runner('Fail')
            steps {
                shell('mvn test')
            }
        }
    }
}

```
