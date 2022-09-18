# Step 01

#### configure shared libraries

navigate to manage jenkins, configure system, find Global Pipeline Libraries, set name i.e shared-libraries, default version i.e main and project/shared-libraries repository i.e https://github.com/darinpope/github-api-global-lib

create a vars folder in the root directory of the shared libraries repository, create a groovy file i.e helloWorld.groovy.
Add the below code snippet in the helloWorld.groovy file.

```yaml

def call(String name, String dayOfWeek) {
    sh "echo Hello ${name}. Today is ${dayOfWeek}."
}
```

# Step 02

#### create a test-shared-libraries pipeline

```yaml
@Library("shared-libraries") _
pipeline {
    agent {
        kubernetes {
            yaml '''
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: shell
                    image: ubuntu
                    command:
                    - sleep
                    args:
                    - infinity
                '''
            defaultContainer 'shell'
        }
    }
    stages {
        stage('call hello world') {
            steps {
                helloWorld("OpeOmotayo", "Thursday")
            }
        }
    }
}
```

#### take note:
shared-libraries in @Library("shared-libraries") _ must match the set name in configure system
The method call name helloWorld(...) matches helloWorld.groovy 

#### references:
https://www.youtube.com/watch?v=Wj-weFEsTb0
