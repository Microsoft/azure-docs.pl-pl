---
title: Ciągłe aktualizowanie kodu aplikacji funkcji przy użyciu Azure DevOps
description: Dowiedz się, jak skonfigurować potok Azure DevOps przeznaczony dla Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a99e313a0c3fe9093137d4acaa64e789ef5e10e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762213"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Ciągłe dostarczanie przy użyciu Azure DevOps

Funkcję można automatycznie wdrożyć w aplikacji Azure Functions przy użyciu [Azure Pipelines](/azure/devops/pipelines/).

Dostępne są dwie opcje definiowania potoku:

- **Plik YAML:** plik YAML opisuje potok. Plik może zawierać sekcję kroków kompilacji i sekcję wydania. Plik YAML musi znajdować się w tym samym repocie co aplikacja.
- **Szablon:** Szablony to gotowe zadania, które skompilowały lub wdrożyły aplikację.

## <a name="yaml-based-pipeline"></a>Potok oparty na technologii YAML

Aby utworzyć potok oparty na technologii YAML, najpierw skompilować aplikację, a następnie wdrożyć aplikację.

### <a name="build-your-app"></a>Kompilowanie aplikacji

Sposób kompilowania aplikacji w Azure Pipelines zależy od języka programowania aplikacji. Każdy język ma określone kroki kompilacji, które tworzą artefakt wdrożenia. Artefakt wdrożenia służy do wdrażania aplikacji funkcji na platformie Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Poniższy przykład umożliwia utworzenie pliku YAML w celu utworzenia aplikacji .NET:

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

Poniższy przykład umożliwia utworzenie pliku YAML w celu utworzenia aplikacji w języku JavaScript:

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

Aby utworzyć plik YAML dla określonej wersji języka Python, możesz użyć jednego z poniższych przykładów. Język Python jest obsługiwany tylko w przypadku aplikacji funkcji działających w systemie Linux.

**Wersja 3.7**

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

**Wersja 3.6**

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

Poniższy przykład umożliwia utworzenie pliku YAML w celu spakowania aplikacji programu PowerShell. Program PowerShell jest obsługiwany tylko w przypadku systemu Windows Azure Functions.

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

W pliku YAML należy uwzględnić jeden z następujących przykładów YAML, w zależności od systemu operacyjnego hostowania.

#### <a name="windows-function-app"></a>Aplikacja funkcji systemu Windows

Aby wdrożyć aplikację funkcji systemu Windows, możesz użyć następującego fragmentu kodu:

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

Aby wdrożyć aplikację funkcji systemu Linux, możesz użyć następującego fragmentu kodu:

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

## <a name="template-based-pipeline"></a>Potok oparty na szablonach

Szablony w Azure DevOps to wstępnie zdefiniowane grupy zadań, które skompilowały lub wdrożyły aplikację.

### <a name="build-your-app"></a>Kompilowanie aplikacji

Sposób kompilowania aplikacji w Azure Pipelines zależy od języka programowania aplikacji. Każdy język ma określone kroki kompilacji, które tworzą artefakt wdrożenia. Artefakt wdrożenia służy do aktualizowania aplikacji funkcji na platformie Azure.

Aby użyć wbudowanych szablonów kompilacji, podczas tworzenia  nowego potoku kompilacji wybierz pozycję Użyj edytora klasycznego, aby utworzyć potok przy użyciu szablonów projektanta.

![Wybieranie Azure Pipelines edytora klasycznego](media/functions-how-to-azure-devops/classic-editor.png)

Po skonfigurowaniu źródła kodu wyszukaj Azure Functions kompilacji. Wybierz szablon, który odpowiada Twojeowi językowi aplikacji.

![Wybieranie szablonu Azure Functions kompilacji](media/functions-how-to-azure-devops/build-templates.png)

W niektórych przypadkach artefakty kompilacji mają określoną strukturę folderów. Może być konieczne zaznaczenie pola wyboru **Prepend root folder name to archive paths (Dołączanie** nazwy folderu głównego do ścieżek archiwum).

![Opcja dołączania nazwy folderu głównego](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Aplikacje JavaScript

Jeśli aplikacja JavaScript jest zależna od modułów natywnych systemu Windows, należy zaktualizować wersję puli agentów do hostowanej wersji **vs2017.**

![Aktualizowanie wersji puli agentów](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Wdrażanie aplikacji

Podczas tworzenia nowego potoku wydania wyszukaj szablon Azure Functions wydania.

![Wyszukiwanie szablonu Azure Functions wersji](media/functions-how-to-azure-devops/release-template.png)

Wdrażanie w miejscu wdrożenia nie jest obsługiwane w szablonie wydania.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Tworzenie potoku kompilacji przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć potok kompilacji na platformie Azure, użyj `az functionapp devops-pipeline create` [polecenia](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create). Potok kompilacji jest tworzony w celu kompilowania i zwalniania wszelkich zmian kodu wprowadzonych w twoim repocie. Polecenie generuje nowy plik YAML, który definiuje potok kompilacji i wydania, a następnie zatwierdza go w twoim repoecie. Wymagania wstępne dla tego polecenia zależą od lokalizacji kodu.

- Jeśli kod znajduje się w usłudze GitHub:

    - Musisz mieć **uprawnienia do zapisu** dla swojej subskrypcji.

    - Musisz być administratorem projektu w Azure DevOps.

    - Musisz mieć uprawnienia do tworzenia osobistego tokenu dostępu usługi GitHub, który ma wystarczające uprawnienia. Aby uzyskać więcej informacji, zobacz Wymagania dotyczące uprawnień do [patowego dostępu usługi GitHub.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Musisz mieć uprawnienia do zatwierdzania w głównej gałęzi w repozytorium GitHub, aby można było zatwierdzić automatycznie wygenerowany plik YAML.

- Jeśli kod znajduje się w Azure Repos:

    - Musisz mieć **uprawnienia do zapisu** dla swojej subskrypcji.

    - Musisz być administratorem projektu w Azure DevOps.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [Azure Functions omówieniem.](functions-overview.md)
- Zapoznaj się [z Azure DevOps omówieniem.](/azure/devops/pipelines/)