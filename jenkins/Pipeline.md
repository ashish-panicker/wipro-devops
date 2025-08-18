# Pipeline

A pipeline is a way to define and automate the entire software delivery process. In jenkins we create
a file named `Jenkinsfile` which is used to define the pipeline. The pipleine will describe
- Where the code should be collected from? (Github, Gitlab)
- What to do with the code? (Build, Test, Package, Deploy)
- What to do on success and failure?

## Types of pipelines

### Decalrative Pipeline

Simple, structured and recommended weay of building pipelines in Jenkins. These are easier to read and 
maintain and we use the `pipeline{...}` block to define these kind of pipelines.

> Basic Structure

```groovy
    pipeline {
        agent any         // where to run
        tools { }         // JDK, Maven, Node, etc.
        environment { }   // env vars
        options { }       // build settings
        triggers { }      // auto triggers
        stages {          // ordered pipeline steps
            stage('Name') {
                steps { } // shell commands or Jenkins steps
                post { }  // actions after stage
            }
        }
        post { }          // actions after whole pipeline
}
```

### Scripted Pipeline

More flexible, uses full groovy code, and suitable for more complex workflows.


## Pipeline: Key concepts

### Stages

High level steps in the pipeline process. Examples: Checkout, Deploy

### Steps

Individual commands in stage. Example: `mvn clean install`

### Post Actions

What to do after a stage is completed? Example: Publish the test report from the build stage

### Triggers

What causes the pipeline to start working? Example: A push into the main branch

### Agent

Node/machine/server on which the pipeline executes.
- Example: `agent any`
- Example: `agent { docker { image 'maven:3.8.4-openjdk-21' } }`

## Pipeline - Build it step by step

1. Identify the workflow
   1. Start by cloning the repo, then build it, then test it, then archive it
2. Map to jenkins stages
   1. Each workflow to a stage
3. Decide on the agent
   1. Jenkins node, or Docker Image
4. Add tools
   1. jdk, maven, git
5. Add post actions
   1. Collect the test report and publish it
6. Add triggers
   1. Gihub push webhooks, cron jobs, manual
7. Make this resuable
   1. use environment variables, params, shared libraries

## Jenkins pipeline with Sonar

1. Install `SonarQuebeScanner` and `Sonar Quality Gate` plugins in Jenkins.
2. Create a [Sonar Cloud Account](https://sonarcloud.io)
3. Generate a security token in [Sonar](https://sonarcloud.io/account/security)
4. Navigate to Jenkins -> Manage -> System -> Sonar Cloud Installations
   1. Name as `Sonar Cloud`
   2. Server URL as `https://sonarcloud.io`
   3. Create a new server authentication token by clicking `Add` -> `Jenkins` -> `Secret Text`
      1. Paste the genrated security token in the Secret field
      2. Provide an id
      3. Click add
   4. Now select the authnetication you just created
5. Now click `Save`