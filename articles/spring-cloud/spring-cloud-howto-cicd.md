---
title: Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure
description: Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d31be45f5cf608b7dfd727741de68544574d7f8a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496461"
---
# <a name="cicd-for-azure-spring-cloud"></a>Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure

Narzędzia ciągłej integracji i ciągłego dostarczania umożliwiają deweloperom szybkie wdrażanie aktualizacji istniejących aplikacji z minimalnym nakładem pracy i ryzykiem. Usługa Azure DevOps pomaga organizować i kontrolować te kluczowe zadania. Obecnie chmura Wiosnowa platformy Azure nie oferuje określonej wtyczki usługi Azure DevOps.  Można jednak zintegrować aplikacje w chmurze wiosny z DevOps za pomocą [zadania interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). W tym artykule opisano sposób korzystania z zadania interfejsu wiersza polecenia platformy Azure z chmurą Azure wiosną w celu integracji z usługą Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Utwórz połączenie usługi Azure Resource Manager

Przeczytaj [ten artykuł](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) , aby dowiedzieć się, jak utworzyć połączenie usługi Azure Resource Manager do projektu usługi Azure DevOps. Pamiętaj, aby wybrać tę samą subskrypcję, która jest używana dla wystąpienia usługi w chmurze ze sprężyną na platformie Azure.

## <a name="azure-cli-task-templates"></a>Szablony zadań interfejsu wiersza polecenia platformy Azure

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
