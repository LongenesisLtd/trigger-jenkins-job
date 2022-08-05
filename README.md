# Trigger Jenkins Job - A GitHub Action

The [Trigger Jenkins Job](https://github.com/mickeygoussetorg/trigger-jenkins-job) is a [composite run step custom action](https://docs.github.com/en/actions/creating-actions/creating-a-composite-run-steps-action) that can be used to trigger a Jenkins job, wait for it to complete, and return the results.  Please make sure to read this README completely, as there are some caveats to using this action.

## Caveats

The following are known issues with this action:

- The action only works on Linux runners
- The Linux runners must have `curl` & `jq` installed
- Currently the action only works with jobs with no parameters. Parameters will be available in a future 
- Jenkins must have [Pipeline: REST API Plugin](https://plugins.jenkins.io/pipeline-rest-api/) installed

## Example Workflow

```
jobs:
  trigger-a-jenkins-job:
    runs-on: ubuntu-latest
    steps:
      - id: triggerjenkinsjob
        uses: LongenesisLtd/trigger-jenkins-pipeline@v1
        with:
          jenkins-url: "${{ secrets.JENKINS_URL }}" # URL of the jenkins server
          jenkins-job: "${{ secrets.JENKINS_JOB }}" # The name of the jenkins job to run
          jenkins-basic-auth: "${{ secrets.JENKINS_TOKEN }}" # basic auth for jenkins
          poll-time: 10 # how often to poll the jenkins server for results
          verbose: true # true/false - turns on verbose logging
          just-trigger: true # true/false - turns on just trigger so you can track stages in different jobs
```

## Input Variables

Variable Name | Description
------------- | -----------
**jenkins-url** | Text string the URL to your Jenkins server. Example: http://myjenkins.acme.com:8080. It is suggested you put this value in a secret.
**jenkins-job** | Text string containing the name of the Jenkins job to execute
**jenkins-basic-auth** | basic auth for jenkins, can use token or password for second part. Store this in a secret for security
**poll-time** | Time, in seconds, of how often the action should poll the Jenkins job to see if it has completed
**verbose** | true/false. This value, when true, enables extra logging in the build output. By default it is false, meaning minimal logging
**just-trigger** | true/false - turns on just trigger so you can track stages in different jobs

## Background [from original author]

For many people who move to using GitHub Enteprise Cloud, but have a strong investment in Jenkins, the ability to continue to use Jenkins is required. However, for most people, Jenkins is not exposed to the Internet, so the ability to create a webhook to, say, trigger a Jenkins job on creation of a pull request is not possible.   We can, however, simulate a webhook by creating a GitHub Actions workflow, and using a self-hosted runner inside a company's network.

This action was designed to be used in that scenario, though it could be used in other scenarios as well.  The action itself is written as a Bash script, which is why a Linux runner is required. It makes use of `curl` commands against the Jenkins API to trigger and poll, hence the need for `curl` to be installed on the runner.

Please feel free to contribute back to this action if you are interested. 