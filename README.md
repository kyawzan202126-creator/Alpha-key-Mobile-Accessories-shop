trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

steps:
- task: UseDotNet@2
  inputs:
    packageType: 'sdk'
    version: '8.0.x'

- script: dotnet restore
  displayName: 'Restore dependencies'

- script: dotnet build --configuration $(buildConfiguration) --no-restore
  displayName: 'Build app'

- script: dotnet test --configuration $(buildConfiguration) --no-build --verbosity normal
  displayName: 'Run tests'

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.SourcesDirectory)/bin/$(buildConfiguration)'
    ArtifactName: 'drop'

- task: AzureWebApp@1
  inputs:
    azureSubscription: 'your-service-connection-name'
    appType: 'webAppLinux'
    appName: 'your-app-service-name'
    package: '$(Build.SourcesDirectory)/**/drop/**/*'