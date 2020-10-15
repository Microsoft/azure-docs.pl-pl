---
title: Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure
description: Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 79d9b2a0d706dd2d9861d068de0e4671db1c5158
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089115"
---
# <a name="cicd-for-azure-spring-cloud"></a>Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure

Narzędzia do ciągłej integracji i ciągłego dostarczania umożliwiają szybkie wdrażanie aktualizacji istniejących aplikacji z minimalnym nakładem pracy i ryzykiem. Usługa Azure DevOps pomaga organizować i kontrolować te kluczowe zadania. Obecnie chmura Wiosnowa platformy Azure nie oferuje określonej wtyczki usługi Azure DevOps.  Można jednak zintegrować aplikacje w chmurze wiosny z DevOps za pomocą [zadania interfejsu wiersza polecenia platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-cli?preserve-view=true&view=azure-devops).

W tym artykule pokazano, jak używać zadania interfejsu wiersza polecenia platformy Azure z chmurą Azure wiosennej w celu integracji z usługą Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Utwórz połączenie usługi Azure Resource Manager

Przeczytaj [ten artykuł](/azure/devops/pipelines/library/connect-to-azure?preserve-view=true&view=azure-devops) , aby dowiedzieć się, jak utworzyć połączenie usługi Azure Resource Manager do projektu usługi Azure DevOps. Pamiętaj, aby wybrać tę samą subskrypcję, która jest używana dla wystąpienia usługi w chmurze ze sprężyną na platformie Azure.

## <a name="azure-cli-task-templates"></a>Szablony zadań interfejsu wiersza polecenia platformy Azure
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Wdróż artefakty

Możesz tworzyć i wdrażać projekty przy użyciu serii `tasks` . Ten fragment kodu definiuje zmienne, zadanie .NET Core do kompilowania aplikacji oraz zadanie interfejsu wiersza polecenia platformy Azure w celu wdrożenia pliku *. zip* .

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --is-public true
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Wdróż artefakty

Możesz tworzyć i wdrażać projekty przy użyciu serii `tasks` . Ten fragment kodu najpierw definiuje zadanie Maven do skompilowania aplikacji, a następnie drugie zadanie, które wdraża plik JAR przy użyciu rozszerzenia interfejsu wiersza polecenia platformy Azure w chmurze ze sprężyną na platformie Azure.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Wdróż ze źródła

Możliwe jest wdrożenie bezpośrednio na platformie Azure bez oddzielnego kroku kompilacji.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny](spring-cloud-quickstart.md)