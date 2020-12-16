---
title: Ciągle Aktualizuj kod aplikacji funkcji za pomocą usługi Azure DevOps
description: Dowiedz się, jak skonfigurować potok Azure DevOps, który jest ukierunkowany na Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a3f423a144738fdaa4462606de6ad4a4e34d6775
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563419"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Ciągłe dostarczanie za pomocą usługi Azure DevOps

Funkcję można automatycznie wdrożyć w aplikacji Azure Functions przy użyciu [Azure Pipelines](/azure/devops/pipelines/).

Dostępne są dwie opcje definiowania potoku:

- **Plik YAML**: plik YAML zawiera opis potoku. Plik może zawierać sekcję kroków kompilacji i sekcję dotyczącą wersji. Plik YAML musi znajdować się w tym samym repozytorium co aplikacja.
- **Szablon**: szablony są gotowymi zadaniami, które kompilują lub wdrażają aplikację.

## <a name="yaml-based-pipeline"></a>Potok oparty na YAML

Aby utworzyć potok oparty na YAML, najpierw skompiluj aplikację, a następnie wdróż aplikację.

### <a name="build-your-app"></a>Kompilowanie aplikacji

Sposób kompilowania aplikacji w Azure Pipelines zależy od języka programowania aplikacji. Każdy język zawiera konkretne kroki kompilacji, które tworzą artefakt wdrożenia. Artefakt wdrożenia służy do wdrażania aplikacji funkcji na platformie Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Możesz użyć poniższego przykładu, aby utworzyć plik YAML w celu skompilowania aplikacji .NET:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Możesz użyć poniższego przykładu, aby utworzyć plik YAML w celu skompilowania aplikacji JavaScript:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Możesz użyć jednego z poniższych przykładów, aby utworzyć plik YAML w celu skompilowania aplikacji dla określonej wersji języka Python. Język Python jest obsługiwany tylko w przypadku aplikacji funkcji działających w systemie Linux.

**Wersja 3,7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Wersja 3,6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Możesz użyć poniższego przykładu, aby utworzyć plik YAML w celu spakowania aplikacji programu PowerShell. Program PowerShell jest obsługiwany tylko w przypadku Azure Functions systemu Windows.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

W pliku YAML należy uwzględnić jeden z następujących przykładów YAML, w zależności od systemu operacyjnego hostingu.

#### <a name="windows-function-app"></a>Aplikacja funkcji systemu Windows

Poniższego fragmentu kodu można użyć do wdrożenia aplikacji funkcji systemu Windows:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Aplikacja funkcji systemu Linux

Poniższego fragmentu kodu można użyć do wdrożenia aplikacji funkcji systemu Linux:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Potok oparty na szablonie

Szablony na platformie Azure DevOps są wstępnie zdefiniowanymi grupami zadań, które kompilują lub wdrażają aplikację.

### <a name="build-your-app"></a>Kompilowanie aplikacji

Sposób kompilowania aplikacji w Azure Pipelines zależy od języka programowania aplikacji. Każdy język zawiera konkretne kroki kompilacji, które tworzą artefakt wdrożenia. Artefakt wdrożenia służy do aktualizowania aplikacji funkcji na platformie Azure.

Aby używać wbudowanych szablonów kompilacji, podczas tworzenia nowego potoku kompilacji, wybierz opcję **Użyj klasycznego edytora** , aby utworzyć potok za pomocą szablonów projektanta.

![Wybierz Azure Pipelines klasycznego edytora](media/functions-how-to-azure-devops/classic-editor.png)

Po skonfigurowaniu źródła kodu poszukaj Azure Functions szablonów kompilacji. Wybierz szablon, który jest zgodny z językiem aplikacji.

![Wybierz szablon kompilacji Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

W niektórych przypadkach artefakty kompilacji mają określoną strukturę folderów. Może być konieczne wybranie pola wyboru **Nazwa folderu głównego dołączania do ścieżki archiwum** .

![Opcja dołączania nazwy folderu głównego](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikacje JavaScript

Jeśli aplikacja JavaScript ma zależność od modułów macierzystych systemu Windows, należy zaktualizować wersję puli agentów do **hostowanej program VS2017**.

![Aktualizowanie wersji puli agentów](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

Podczas tworzenia nowego potoku wydania Wyszukaj szablon Azure Functions wydania.

![Wyszukaj szablon wydania Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Wdrażanie w miejscu wdrożenia nie jest obsługiwane w szablonie zlecenia.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Tworzenie potoku kompilacji przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć potok kompilacji na platformie Azure, użyj `az functionapp devops-pipeline create` [polecenia](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Potok kompilacji jest tworzony w celu kompilowania i zwalniania wszelkich zmian kodu wprowadzonych w repozytorium. Polecenie generuje nowy plik YAML, który definiuje potok kompilacji i wydania, a następnie zatwierdza go do repozytorium. Wymagania wstępne dotyczące tego polecenia zależą od lokalizacji kodu.

- Jeśli Twój kod znajduje się w serwisie GitHub:

    - Musisz mieć uprawnienia do **zapisu** dla Twojej subskrypcji.

    - Musisz być administratorem projektu w usłudze Azure DevOps.

    - Musisz mieć uprawnienia do tworzenia osobistego tokenu dostępu usługi GitHub, który ma wystarczające uprawnienia. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące uprawnień w witrynie GitHub.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Musisz mieć uprawnienia do zatwierdzania głównej gałęzi w repozytorium GitHub, aby można było zatwierdzić wygenerowany automatycznie plik YAML.

- Jeśli Twój kod jest w Azure Repos:

    - Musisz mieć uprawnienia do **zapisu** dla Twojej subskrypcji.

    - Musisz być administratorem projektu w usłudze Azure DevOps.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem Azure Functions](functions-overview.md).
- Zapoznaj się z [omówieniem usługi Azure DevOps](/azure/devops/pipelines/).