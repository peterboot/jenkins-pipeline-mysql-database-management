# jenkins-pipeline-mysql-database-management
A shared library of Jenkins Pipeline steps to help setup and teardown MySQL test databases. Customization parameters are provided, but if all you care about is creating a test database and removing it after you're done with it, you can use the defaults and be fine.

# NOTE: THIS LIBRARY IS STILL UNDER HEAVY DEVELOPMENT, IT SHOULD NOT BE USED IN LIVE PROJECTS YET

## Steps available
This shared library makes the following steps available to your Declarative Pipeline scripts:


### buildTestMySQLDatabase
This step will build a new test database for your Pipeline to use, as well as adding a test user with full privileges on that database. The name of the test database and the test user will be returned in a Groovy map.

**Usage**
```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Setup the database
                script {
                    withCredentials([string(credentialsId: 'jenkins-database-username', variable: 'DATABASE_USERNAME')]) {
                        withCredentials([string(credentialsId: 'jenkins-database-password', variable: 'DATABASE_PASSWORD')]) {
                            def test_database_credentials = buildTestMySQLDatabase {
                                dbUser = env.DATABASE_USERNAME
                                dbPass = env.DATABASE_PASSWORD
                            }
                            echo 'Test Database Name: ' + test_database_credentials.dbName
                            echo 'Test Username: ' + test_database_credentials.testUsername
                            echo 'Test User Password: ' + test_database_credentials.testUserPassword
                        }
                    }
                }
            }
        }
    }
}
```


### destroyTestMySQLDatabase
This step will destroy the test database when it is no longer needed, also removing the test user and revoking all privileges it had.

**Usage**
```
pipeline {
    agent any

    stages {
        // ...Pipeline steps here...
    }
    post {
        always {
            destroyTestMySQLDatabase {
                dbUser = credentials('jenkins-database-username')
                dbPass = credentials('jenkins-database-password')
            }
        }
    }
}
```
