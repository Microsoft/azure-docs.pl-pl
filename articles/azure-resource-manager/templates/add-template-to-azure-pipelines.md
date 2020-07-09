---
title: Ciągłej integracji/ciągłego wdrażania za pomocą Azure Pipelines i szablonów
description: Opisuje sposób konfigurowania ciągłej integracji w Azure Pipelines przy użyciu projektów wdrażania grupy zasobów platformy Azure w programie Visual Studio w celu wdrażania szablonów Menedżer zasobów.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084655"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrowanie szablonów ARM z Azure Pipelines

Program Visual Studio udostępnia projekt grupy zasobów platformy Azure na potrzeby tworzenia szablonów Azure Resource Manager (ARM) i wdrażania ich w ramach subskrypcji platformy Azure. Można zintegrować ten projekt z Azure Pipelines na potrzeby ciągłej integracji i ciągłego wdrażania (CI/CD).

Istnieją dwa sposoby wdrażania szablonów przy użyciu Azure Pipelines:

* **Dodaj zadanie, które uruchamia skrypt Azure PowerShell**. Ta opcja ma zalety zapewnienia spójności w całym cyklu życia, ponieważ jest używany ten sam skrypt, który jest zawarty w projekcie programu Visual Studio (Deploy-AzureResourceGroup.ps1). Etapy skryptu są artefaktami z projektu do konta magazynu, do którego Menedżer zasobów może uzyskać dostęp. Artefakty to elementy w projekcie, takie jak połączone szablony, skrypty i pliki binarne aplikacji. Następnie skrypt wdraża szablon.

* **Dodawanie zadań do kopiowania i wdrażania zadań**. Ta opcja oferuje wygodną alternatywę dla skryptu projektu. W potoku konfiguruje się dwa zadania. Jednym etapem zadania artefakty, a inne zadanie wdraża szablon.

W tym artykule przedstawiono oba podejścia.

## <a name="prepare-your-project"></a>Przygotowywanie projektu

W tym artykule przyjęto założenie, że projekt programu Visual Studio i organizacja usługi Azure DevOps są gotowe do tworzenia potoku. W poniższych krokach pokazano, jak upewnić się, że wszystko jest gotowe:

* Masz organizację usługi Azure DevOps. Jeśli go nie masz, [Utwórz go bezpłatnie](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Jeśli Twój zespół ma już organizację usługi Azure DevOps, upewnij się, że jesteś administratorem projektu usługi Azure DevOps, którego chcesz użyć.

* Skonfigurowano [połączenie usługi](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) z subskrypcją platformy Azure. Zadania w potoku są wykonywane w ramach tożsamości nazwy głównej usługi. Aby uzyskać instrukcje dotyczące tworzenia połączenia, zobacz [Tworzenie projektu DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Masz projekt programu Visual Studio, który został utworzony na podstawie początkowego szablonu **grupy zasobów platformy Azure** . Aby uzyskać informacje na temat tworzenia tego typu projektu, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](create-visual-studio-deployment-project.md).

* Projekt programu Visual Studio jest [połączony z projektem usługi Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Tworzenie potoku

1. Jeśli potok nie został wcześniej dodany, należy utworzyć nowy potok. W organizacji usługi Azure DevOps wybierz pozycję **potoki** i **Nowy potok**.

   ![Dodaj nowy potok](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Określ miejsce przechowywania kodu. Na poniższej ilustracji przedstawiono Wybieranie **Azure Repos git**.

   ![Wybierz źródło kodu](./media/add-template-to-azure-pipelines/select-source.png)

1. Z tego źródła wybierz repozytorium, które ma kod dla projektu.

   ![Wybieranie repozytorium](./media/add-template-to-azure-pipelines/select-repo.png)

1. Wybierz typ potoku, który ma zostać utworzony. Możesz wybrać pozycję **potok początkowy**.

   ![Wybierz potok](./media/add-template-to-azure-pipelines/select-pipeline.png)

Możesz dodać zadanie Azure PowerShell lub skopiować plik i wdrożyć zadania.

## <a name="azure-powershell-task"></a>Azure PowerShell, zadanie

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu pojedynczego zadania, które uruchamia skrypt programu PowerShell w projekcie. Następujący plik YAML tworzy [zadanie Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

Po ustawieniu zadania na `AzurePowerShell@3` , potok używa poleceń z modułu AzureRM do uwierzytelniania połączenia. Domyślnie skrypt programu PowerShell w projekcie programu Visual Studio używa modułu AzureRM. Jeśli skrypt został zaktualizowany tak, aby korzystał z [modułu AZ module](/powershell/azure/new-azureps-module-az), Ustaw zadanie na `AzurePowerShell@4` .

```yaml
steps:
- task: AzurePowerShell@4
```

W polu `azureSubscription` Podaj nazwę utworzonego połączenia z usługą.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

W przypadku `scriptPath` , podaj ścieżkę względną z pliku potoku do skryptu. Możesz wyszukać w repozytorium, aby zobaczyć ścieżkę.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Jeśli nie musisz przemieścić artefaktów, po prostu przekaż nazwę i lokalizację grupy zasobów, która ma zostać użyta do wdrożenia. Skrypt programu Visual Studio tworzy grupę zasobów, jeśli jeszcze nie istnieje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Jeśli musisz przemieścić artefakty na istniejącym koncie magazynu, użyj:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Teraz, gdy zrozumiesz, jak utworzyć zadanie, przejdźmy do instrukcji, aby przeprowadzić edycję potoku.

1. Otwórz potok i Zastąp jego zawartość YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Wybierz pozycję **Zapisz**.

   ![Zapisywanie potoku](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Podaj komunikat dotyczący zatwierdzenia i przekaż go bezpośrednio do serwera **głównego**.

1. Po wybraniu opcji **Zapisz**potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obejrzyj stan.

   ![Wyświetlanie wyników](./media/add-template-to-azure-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="copy-and-deploy-tasks"></a>Kopiowanie i wdrażanie zadań

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu dwóch zadań do przygotowania artefaktów i wdrożenia szablonu.

W poniższym YAML przedstawiono [zadanie kopiowania plików platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Istnieje kilka części tego zadania, które można poprawić w danym środowisku. `SourcePath`Wskazuje lokalizację artefaktów względem pliku potoku. W tym przykładzie pliki znajdują się w folderze o nazwie, `AzureResourceGroup1` która jest nazwą projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

W polu `azureSubscription` Podaj nazwę utworzonego połączenia z usługą.

```yaml
azureSubscription: '<your-connection-name>'
```

W polu Nazwa magazynu i kontenera Podaj nazwy konta magazynu i kontenera, które mają być używane do przechowywania artefaktów. Konto magazynu musi istnieć.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

W poniższym YAML przedstawiono [zadanie wdrażania szablonu Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Istnieje kilka części tego zadania, które można poprawić w danym środowisku.

- `deploymentScope`: Wybierz zakres wdrożenia z opcji: `Management Group` `Subscription` i `Resource Group` . Użyj **grupy zasobów** w tym instruktażu. Aby dowiedzieć się więcej o zakresach, zobacz sekcję [Deployment Scopes](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Podaj nazwę utworzonego połączenia z usługą.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Podaj identyfikator subskrypcji docelowej. Ta właściwość dotyczy tylko zakresu wdrożenia grupy zasobów i zakresu wdrożenia subskrypcji.

- `resourceGroupName`i `location` : Podaj nazwę i lokalizację grupy zasobów, w której chcesz wdrożyć. Zadanie tworzy grupę zasobów, jeśli nie istnieje.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Zadanie wdrażania łączy się z szablonem o nazwie `WebSite.json` i plikiem parametrów o nazwie WebSite.parameters.json. Użyj nazw szablonu i plików parametrów.

Teraz, gdy zrozumiesz, jak tworzyć zadania, przejdźmy do kroków, aby edytować potok.

1. Otwórz potok i Zastąp jego zawartość YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Wybierz pozycję **Zapisz**.

1. Podaj komunikat dotyczący zatwierdzenia i przekaż go bezpośrednio do serwera **głównego**.

1. Po wybraniu opcji **Zapisz**potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obejrzyj stan.

   ![Wyświetlanie wyników](./media/add-template-to-azure-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje krok po kroku dotyczące korzystania z Azure Pipelines z szablonami ARM, zobacz [Samouczek: Ciągła integracja szablonów Azure Resource Manager z Azure Pipelines](deployment-tutorial-pipeline.md).
