<h1>Jenkinsfile Strategies</h1>
* Below I'll be listing some strategies that have helped me create more efficient pipelines

<h2>Jenkinsfile Reload</h2>
* This is a stage you can add to your Jenkinsfile that will reload the file without running any subsequent stages
  - For situations where you have added or removed a stage

```groovy
parameters {
        booleanParam(name: 'RELOAD_JOB', defaultValue: false, description: 'Reload job from Jenkinsfile and exit')
}
stages{
        stage('Reload Jenkinsfile') {
            when {
                expression { params.RELOAD_JOB == true }
            }
            steps {
                script {
                    echo "Jenkins job reloaded with latest jenkinsfile changes"
                    currentBuild.getRawBuild().getExecutor().interrupt(Result.SUCCESS)
                    sleep(1)
                }
            }
        }
...
```

<h2>Iterate over secretFile to create EnvVars</h2>
* This will iterate over a Jenkins Secret File and store each key/pair as an environment variable

```groovy
stage('Read property file') {
    steps {
         script{
            withCredentials([file(credentialsId: 'xxxx', variable: 'CREDS_FILE')]) {
                def props = readProperties interpolate: true, file: "$CREDS_FILE"
                props.each { key, value ->
                env."${key}" = value
                }
            }
        }
    }
}
...
```

<h2>Iterate over defined JSON</h2>
* I found this to be a neat replacement for using property files for to loop over multiple `withCredentials`
* I've also used this to create loops that iterate over anything pertaining to loops

```groovy
# iterates over env that use separate credentials
def envs = [
    env1: [
        cred_id: 'envCredId'
    ],
    env2: [
        cred_id: 'envCredId',
    ],
]
...
    stages {
        stage("Iterate Test") {
            steps {
                script {
                    # this function iterates over the keys in the defined JSON
                    for (e in envs.keySet()) {
                        env.app_env = e
                        withCredentials([string(credentialsId: envs[app_env].cred_id, variable: 'CRED')]) {
                            sh '''#!/usr/bin/env bash
                                # echos key
                                echo "${app_env}"
                            '''
                        }
                    }
                }
            }
        }
   }
...
```

<h2>Global Incremental Variables</h2>
* This is what I used to define incremental variables that can be called and manipulated from any stage

```groovy
def vars = []
...
stages {
    stage ("Manipulate Var") {
        step {
            script {
                vars += "test"
            }
        }
    }
    stage ("Call Var") {
        step {
            script {
                // iterate over items added into var
                for (var in vars) {
                // add code
                }
            }
        }
    }
}
...
```

<h2>Adding HyperLinks</h2>
* Not a tip, but something I'd like to remember

```groovy
// Define Links
def link1 = "https://google.com"
message: "please click <${link1}|here> for context - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)",
```

<h2>CleanWs</h2>
* Not necessarily a tip, but I always forget it

```groovy
post {
    always {
        cleanWs()
    }
}
```
