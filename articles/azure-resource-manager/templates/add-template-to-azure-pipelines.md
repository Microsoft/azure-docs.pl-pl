---
title: Ciągła integracja/dysk CD z potokami i szablonami platformy Azure
description: W tym artykule opisano sposób konfigurowania ciągłej integracji w potokach platformy Azure przy użyciu projektów wdrażania grupy zasobów platformy Azure w programie Visual Studio w celu wdrożenia szablonów Usługi Resource Manager.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084655"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integruj szablony ARM z potokami platformy Azure

Program Visual Studio udostępnia projekt grupy zasobów platformy Azure do tworzenia szablonów usługi Azure Resource Manager (ARM) i wdrażania ich w ramach subskrypcji platformy Azure. Można zintegrować ten projekt z usługi Azure Potoki dla ciągłej integracji i ciągłego wdrażania (CI/CD).

Istnieją dwa sposoby wdrażania szablonów za pomocą usługi Azure Pipelines:

* **Dodaj zadanie, w które działa skrypt programu Azure PowerShell**. Ta opcja ma tę zaletę, zapewniając spójność w całym cyklu życia rozwoju, ponieważ używasz tego samego skryptu, który jest uwzględniony w projekcie programu Visual Studio (Deploy-AzureResourceGroup.ps1). Skrypt etapy artefaktów z projektu do konta magazynu, które Menedżer zasobów może uzyskać dostęp. Artefakty są elementami w projekcie, takimi jak połączone szablony, skrypty i pliki binarne aplikacji. Następnie skrypt wdraża szablon.

* **Dodawanie zadań do kopiowania i wdrażania zadań**. Ta opcja oferuje wygodną alternatywę dla skryptu projektu. Skonfiguruj dwa zadania w potoku. Jedno zadanie etapy artefaktów, a inne wdrożenie szablonu.

W tym artykule przedstawiono oba podejścia.

## <a name="prepare-your-project"></a>Przygotowanie projektu

W tym artykule założono, że projekt programu Visual Studio i organizacja Azure DevOps są gotowe do utworzenia potoku. Poniższe kroki pokazują, jak upewnić się, że jesteś gotowy:

* Masz organizację Azure DevOps. Jeśli go nie masz, [utwórz go za darmo.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Jeśli Twój zespół ma już organizację Azure DevOps, upewnij się, że jesteś administratorem projektu Azure DevOps, którego chcesz użyć.

* Skonfigurowano [połączenie usługi z](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) subskrypcją platformy Azure. Zadania w potoku wykonywane w ramach tożsamości jednostki usługi. Aby uzyskać instrukcje tworzenia połączenia, zobacz [Tworzenie projektu DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Masz projekt programu Visual Studio, który został utworzony na podstawie szablonu startowego **grupy zasobów platformy Azure.** Aby uzyskać informacje dotyczące tworzenia tego typu projektu, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pośrednictwem programu Visual Studio](create-visual-studio-deployment-project.md).

* Twój projekt programu Visual Studio jest [połączony z projektem programu Azure DevOps.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Tworzenie potoku

1. Jeśli potok nie został wcześniej dodany, należy utworzyć nowy potok. W organizacji Azure DevOps wybierz pozycję **Potoki** i **Nowy potok**.

   ![Dodawanie nowego potoku](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Określ, gdzie kod jest przechowywany. Na poniższej ilustracji przedstawiono wybranie **opcji Azure Repos Git**.

   ![Wybierz źródło kodu](./media/add-template-to-azure-pipelines/select-source.png)

1. Z tego źródła wybierz repozytorium, które ma kod dla projektu.

   ![Wybierz repozytorium](./media/add-template-to-azure-pipelines/select-repo.png)

1. Wybierz typ potoku do utworzenia. Można wybrać **Potok startowy**.

   ![Wybierz potok](./media/add-template-to-azure-pipelines/select-pipeline.png)

Możesz dodać zadanie programu Azure PowerShell lub skopiować plik i wdrożyć zadania.

## <a name="azure-powershell-task"></a>Zadanie programu Azure PowerShell

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu jednego zadania, które uruchamia skrypt programu PowerShell w projekcie. Następujący plik YAML tworzy [zadanie programu Azure PowerShell:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

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

Po ustawieniu `AzurePowerShell@3`zadania, potok używa poleceń z modułu AzureRM do uwierzytelniania połączenia. Domyślnie skrypt programu PowerShell w projekcie programu Visual Studio używa modułu AzureRM. Jeśli skrypt został zaktualizowany w celu użycia [modułu Az,](/powershell/azure/new-azureps-module-az)ustaw zadanie na `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Dla `azureSubscription`, podaj nazwę utworzonego połączenia usługi.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Dla `scriptPath`, podaj ścieżkę względną z pliku potoku do skryptu. Możesz spojrzeć w repozytorium, aby zobaczyć ścieżkę.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Jeśli nie trzeba wystawiać artefaktów, po prostu przekaż nazwę i lokalizację grupy zasobów do użycia do wdrożenia. Skrypt programu Visual Studio tworzy grupę zasobów, jeśli jeszcze nie istnieje.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Jeśli chcesz zorganizować artefakty na istniejącym koncie magazynu, użyj:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Teraz, gdy zrozumiesz, jak utworzyć zadanie, przejdźmy przez kroki, aby edytować potok.

1. Otwórz potok i zastąp zawartość yaml:

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

1. Podaj wiadomość dla zatwierdzenia i zaobowiąż się bezpośrednio do **wzorca**.

1. Po wybraniu **opcji Zapisz**potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obserwuj stan.

   ![Wyświetlanie wyników](./media/add-template-to-azure-pipelines/view-results.png)

Można wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="copy-and-deploy-tasks"></a>Kopiowanie i wdrażanie zadań

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu dwóch zadań do etapu artefaktów i wdrażania szablonu.

Następujący yaml pokazuje [zadanie kopiowania pliku platformy Azure:](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)

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

Istnieje kilka części tego zadania do zmiany dla środowiska. Wskazuje `SourcePath` lokalizację artefaktów względem pliku potoku. W tym przykładzie pliki istnieją `AzureResourceGroup1` w folderze o nazwie, który był nazwą projektu.

```yaml
SourcePath: '<path-to-artifacts>'
```

Dla `azureSubscription`, podaj nazwę utworzonego połączenia usługi.

```yaml
azureSubscription: '<your-connection-name>'
```

W przypadku przechowywania i nazwy kontenera podaj nazwy konta magazynu i kontenera, którego chcesz użyć do przechowywania artefaktów. Konto magazynu musi istnieć.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Następujący plik YAML przedstawia [zadanie wdrażania szablonu usługi Azure Resource Manager:](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

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

Istnieje kilka części tego zadania do zmiany dla środowiska.

- `deploymentScope`: Wybierz zakres wdrożenia z `Management Group`opcji: , `Subscription` i `Resource Group`. Użyj **grupy zasobów** w tym spacerze. Aby dowiedzieć się więcej o zakresach, zobacz [Zakresy wdrażania](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Podaj nazwę utworzonego połączenia usługi.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Podaj identyfikator subskrypcji docelowej. Ta właściwość ma zastosowanie tylko do zakresu wdrażania grupy zasobów i zakresu wdrażania subskrypcji.

- `resourceGroupName`oraz `location`: podaj nazwę i lokalizację grupy zasobów, do której chcesz wdrożyć. Zadanie tworzy grupę zasobów, jeśli nie istnieje.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Zadanie wdrażania łączy się `WebSite.json` z szablonem o nazwie i plikiem parametrów o nazwie WebSite.parameters.json. Użyj nazw plików szablonów i parametrów.

Teraz, gdy zrozumiesz, jak utworzyć zadania, przejdźmy przez kroki, aby edytować potok.

1. Otwórz potok i zastąp zawartość yaml:

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

1. Podaj wiadomość dla zatwierdzenia i zaobowiąż się bezpośrednio do **wzorca**.

1. Po wybraniu **opcji Zapisz**potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obserwuj stan.

   ![Wyświetlanie wyników](./media/add-template-to-azure-pipelines/view-results.png)

Można wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać krok po kroku proces korzystania z usługi Azure Potoki z szablonami ARM, zobacz [Samouczek: Ciągła integracja szablonów usługi Azure Resource Manager z potokami platformy Azure.](deployment-tutorial-pipeline.md)
