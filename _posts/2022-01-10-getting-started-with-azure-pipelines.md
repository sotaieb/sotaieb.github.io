---
title: "Getting Started With Azure Pipelines"
categories:
  - Devops
tags:
  - Devops
  - Azure Pipelines
---

- Continuous Integration (CI) is the practice used by development teams of automating merging and testing code.
- Continuous Delivery (CD) is a process by which code is built, tested, and deployed to one or more test and production environments.

## Pipelines Overview

A pipeline run includes jobs, steps, and tasks.

- A trigger tells a Pipeline to run.
- A pipeline is made up of one or more stages (environment deployments)
- Each job runs on one agent.
- Each agent runs a job that contains one or more steps.
- A step can be a task or script.

## Define resources

Resources in YAML represent sources of pipelines, builds,
repositories, containers, packages, and webhooks.

When you define a resource, it can be consumed anywhere in your pipeline.
You can automate your DevOps workflow by subscribing to trigger events
on your resources.
resources:
pipelines: [ pipeline ]  
 builds: [ build ]
repositories: [ repository ]
containers: [ container ]
packages: [ package ]
webhooks: [ webhook ]

## Use Variables

### Predefined variables

Ex.
steps:

- bash: echo This script could use $SYSTEM_ACCESSTOKEN
  env:
  SYSTEM_ACCESSTOKEN: $(System.AccessToken)
- powershell: |
  Write-Host "This is a script that could use $env:SYSTEM_ACCESSTOKEN"
      Write-Host "$env:SYSTEM_ACCESSTOKEN = $(System.AccessToken)"
  env:
  SYSTEM_ACCESSTOKEN: $(System.AccessToken)

Build.ArtifactStagingDirectory => The local path on the agent where any artifacts are copied (c:\agent_work\1\a)
System.Debug => bool
Agent.BuildDirectory
Agent.HomeDirectory (Ex.c:\agent)
AGENT_JOBSTATUS =>Canceled,Failed,Succeeded,SucceededWithIssues
Build.BuildNumber
Build.DefinitionName (The version of the build pipeline)
Build.QueuedBy
Build.Reason: Manual,IndividualCI,Schedule,PullRequest,ResourceTrigger (triggered by another build)
Build.Repository.LocalPath => The local path on the agent where your source code files are downloaded. (c:\agent_work\1\s)
Build.SourcesDirectory
Build.StagingDirectory
Build.Repository.Name
Build.SourceBranch => (refs/heads/master,refs/pull/1/merge,refs/tags/your-tag-name
Build.SourceBranchName
Pipeline.Workspace => th same as Agent.BuildDirectory
Environment.Name
System.DefaultWorkingDirectory
System.JobName
System.StageName
System.PullRequest.PullRequestId
System.PullRequest.SourceBranch
System.PullRequest.TargetBranch

## Use Parameters

Unlike variables, pipeline parameters can't be changed
by a pipeline while it's running.

parameters:

- name: configs
  type: string
  default: 'x86,x64'
  jobs:
- ${{ if contains(parameters.configs, 'x86') }}:
  - job: x86
    steps:
    - script: echo Building x86...

## Change the platform to build on

pool:
vmImage: "windows-latest"

You can build and test your project on multiple platforms.
your build run up to three jobs on three different platforms.
strategy:
matrix:
linux:
imageName: "ubuntu-latest"
mac:
imageName: "macOS-latest"
windows:
imageName: "windows-latest"
maxParallel: 3

pool:
vmImage: $(imageName)

## Customize CI triggers

Azure Pipelines provides several types of triggers:

- Scheduled triggers: Run your pipelines based on a schedule.
  (minute-hour-dayofmonth-month-dayofweek)([*]:any-[,]:values,[-]: range,[/]: step value)
- Event-based triggers: start your pipeline in response to events, such as creating a pull request or pushing to a branch.
  The following example defines two schedules:
  schedules:
- cron: "0 0 \* \* \*"
  displayName: Daily midnight build
  branches:
  include:
  - main
  - releases/\*
    exclude:
  - releases/ancient/\*
- cron: "0 12 \* \* 0"
  displayName: Weekly Sunday build
  branches:
  include:
  - releases/\*
    always: true

Pipeline triggers cause a pipeline to run.
You can set up triggers for specific branches or for pull request
validation.
trigger:

- main
- releases/\*
  pr:
- main
- releases/\*

## Use Templates

Templates are a commonly used feature in YAML pipelines.
They are an easy way to share pipeline snippets.

steps:

- template: xxx.yml

## Configure run or build numbers:

Ex1.
name: $(TeamProject)_$(Build.DefinitionName)_$(SourceBranchName)_$(Date:yyyyMMdd)$(Rev:.r)

steps:

- script: echo '$(Build.BuildNumber)' # outputs customized build number like project_def_master_20200828.1

variables:
${{ if eq(variables['Build.Reason'], 'PullRequest') }}:
    why: pr
  ${{ elseif eq(variables['Build.Reason'], 'Manual' ) }}:
    why: manual
  ${{ elseif eq(variables['Build.Reason'], 'IndividualCI' ) }}:
    why: indivci 
  ${{ else }}:
    why: other
Ex. 2
name: $(TeamProject)_$(SourceBranchName)_$(why)_$(Date:yyyyMMdd)$(Rev:.r)

pool:
vmImage: 'ubuntu-latest'

steps:

- script: echo '$(Build.BuildNumber)' ## output run number

## Define container jobs (YAML)

This tells the system to fetch the ubuntu image tagged 18.04 from Docker Hub and then start the container. When the printenv command runs,
it will happen inside the ubuntu:18.04 container.
pool:
vmImage: 'ubuntu-18.04'

container: ubuntu:18.04

steps:

- script: printenv

## Publish Nuget Task

You must first create a service connection to connect to that feed.
Project settings > Service connections > New service connection
task: DotNetCoreCLI@2
inputs:
command: pack
versioningScheme: byPrereleaseNumber
majorVersion: '$(Major)'
    minorVersion: '$(Minor)'
patchVersion: '$(Patch)'
steps:

- task: NuGetAuthenticate@0
  inputs:
  nuGetServiceConnections: '<NAME_OF_YOUR_NUGET_SERVICE_CONNECTION>'
- task: NuGetCommand@2
  displayName: 'NuGet push'
  inputs:
  command: push
  publishVstsFeed: 'projectName/feed'
  allowPackageConflicts: true

## Powershell Task

- task: PowerShell@2
  inputs:
  #targetType: 'filePath' # Optional. Options: filePath, inline
  #filePath: # Required when targetType == FilePath
  #arguments: # Optional
  #script: '# Write your PowerShell commands here.Write-Host Hello World' # Required when targetType == Inline
  #errorActionPreference: 'stop' # Optional. Options: default, stop, continue, silentlyContinue
  #warningPreference: 'default' # Optional. Options: default, stop, continue, silentlyContinue
  #informationPreference: 'default' # Optional. Options: default, stop, continue, silentlyContinue
  #verbosePreference: 'default' # Optional. Options: default, stop, continue, silentlyContinue
  #debugPreference: 'default' # Optional. Options: default, stop, continue, silentlyContinue
  #failOnStderr: false # Optional
  #ignoreLASTEXITCODE: false # Optional
  #pwsh: false # Optional
  #workingDirectory: # Optional
  test.ps1
  param ($input1, $input2)

Write-Host "$input1 $input2"

- task: PowerShell@2
  inputs:
  targetType: 'filePath'
  filePath: $(System.DefaultWorkingDirectory)\test.ps1
  arguments: > # Use this to avoid newline characters in multiline string
  -input1 "Hello"
  -input2 "World"
  displayName: 'Print Hello World'

## Status Badge

Azure Pipelines->Pipeline->Status Badge

## Unparented

When manually running the pipeline, you can select whether it succeeds or fails.

```yaml
parameters:

- name: succeed
  displayName: Succeed or fail
  type: boolean
  default: false

trigger:

- main

pool:
vmImage: ubuntu-latest

jobs:

- job: Work
  steps:

  - script: echo Hello, world!
    displayName: 'Run a one-line script'

  # This malformed command causes the job to fail

  # Only run this command if the succeed variable is set to false

  - script: git clone malformed input
    condition: eq(${{ parameters.succeed }}, false)

# This job creates a work item, and only runs if the previous job failed

- job: ErrorHandler
  dependsOn: Work
  condition: failed()
  steps:
  - bash: |
    az boards work-item create \
     --title "Build $(build.buildNumber) failed" \
     --type bug \
     --org $(System.TeamFoundationCollectionUri) \
     --project $(System.TeamProject)
    env:
    AZURE_DEVOPS_EXT_PAT: $(System.AccessToken)
    displayName: 'Create work item on failure'
```
