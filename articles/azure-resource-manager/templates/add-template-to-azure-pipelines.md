---
title: Ciągłej integracji/ciągłego wdrażania za pomocą Azure Pipelines i szablonów
description: Opisuje sposób konfigurowania ciągłej integracji w programie Azure Pipelines przy użyciu szablonów Azure Resource Manager. Pokazano, jak używać skryptu programu PowerShell lub skopiować pliki do lokalizacji tymczasowej i wdrożyć je stamtąd.
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 4a2f1f15de0abd802f3dce138b2cea33e52e3dfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561946"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrowanie szablonów usługi ARM z usługą Azure Pipelines

Szablony Azure Resource Manager (szablony ARM) można zintegrować z Azure Pipelines na potrzeby ciągłej integracji i ciągłego wdrażania (CI/CD). W tym artykule przedstawiono dwa bardziej zaawansowane sposoby wdrażania szablonów przy użyciu Azure Pipelines.

## <a name="select-your-option"></a>Wybierz opcję

Przed przejściem do tego artykułu Rozważmy różne opcje wdrażania szablonu ARM z potoku.

* **Użyj zadania wdrażania szablonu ARM**. Ta opcja jest najłatwiejszym rozwiązaniem. To podejście działa, gdy chcesz wdrożyć szablon bezpośrednio z repozytorium. Ta opcja nie została omówiona w tym artykule, ale jest omówiona w samouczku [ciągła integracja szablonów ARM z Azure Pipelines](deployment-tutorial-pipeline.md). Pokazano, jak wdrożyć szablon z repozytorium GitHub przy użyciu [zadania wdrażania szablonu ARM](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) .

* **Dodaj zadanie, które uruchamia skrypt Azure PowerShell**. Ta opcja ma zalety zapewnienia spójności w całym cyklu życia programistycznego, ponieważ można użyć tego samego skryptu, który został użyty podczas uruchamiania testów lokalnych. Skrypt wdraża szablon, ale może również wykonywać inne operacje, takie jak pobieranie wartości jako parametrów. Ta opcja jest pokazana w tym artykule. Zobacz [Azure PowerShell zadanie](#azure-powershell-task).

   Program Visual Studio udostępnia [projekt grupy zasobów platformy Azure](create-visual-studio-deployment-project.md) , który zawiera skrypt programu PowerShell. Etapy skryptu są artefaktami z projektu do konta magazynu, do którego Menedżer zasobów może uzyskać dostęp. Artefakty to elementy w projekcie, takie jak połączone szablony, skrypty i pliki binarne aplikacji. Jeśli chcesz kontynuować używanie skryptu z projektu, Użyj zadania skryptu programu PowerShell pokazanego w tym artykule.

* **Dodawanie zadań do kopiowania i wdrażania zadań**. Ta opcja oferuje wygodną alternatywę dla skryptu projektu. W potoku konfiguruje się dwa zadania. Jedno zadanie etapuje artefakty do dostępnej lokalizacji. Inne zadanie wdraża szablon z tej lokalizacji. Ta opcja jest pokazana w tym artykule. Zobacz [kopiowanie i wdrażanie zadań](#copy-and-deploy-tasks).

## <a name="prepare-your-project"></a>Przygotowywanie projektu

W tym artykule przyjęto założenie, że szablon ARM i organizacja usługi Azure DevOps są gotowe do tworzenia potoku. W poniższych krokach pokazano, jak upewnić się, że wszystko jest gotowe:

* Masz organizację usługi Azure DevOps. Jeśli go nie masz, [Utwórz go bezpłatnie](/azure/devops/pipelines/get-started/pipelines-sign-up). Jeśli Twój zespół ma już organizację usługi Azure DevOps, upewnij się, że jesteś administratorem projektu usługi Azure DevOps, którego chcesz użyć.

* Skonfigurowano [połączenie usługi](/azure/devops/pipelines/library/connect-to-azure) z subskrypcją platformy Azure. Zadania w potoku są wykonywane w ramach tożsamości nazwy głównej usługi. Aby uzyskać instrukcje dotyczące tworzenia połączenia, zobacz [Tworzenie projektu DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* Masz [szablon ARM](quickstart-create-templates-use-visual-studio-code.md) , który definiuje infrastrukturę dla projektu.

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

## <a name="azure-powershell-task"></a>Zadanie programu Azure PowerShell

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu pojedynczego zadania, które uruchamia skrypt programu PowerShell w projekcie. Jeśli potrzebujesz skryptu programu PowerShell, który wdraża szablon, zobacz [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) lub [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

Następujący plik YAML tworzy [zadanie Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Po ustawieniu zadania na `AzurePowerShell@5` , potok używa [AZ module](/powershell/azure/new-azureps-module-az). Jeśli używasz modułu AzureRM w skrypcie, Ustaw zadanie na `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
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

W programie `ScriptArguments` Podaj wszelkie parametry potrzebne przez skrypt. W poniższym przykładzie przedstawiono niektóre parametry skryptu, ale konieczne będzie dostosowanie parametrów dla skryptu.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Po wybraniu opcji **Zapisz** potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obejrzyj stan.

![Wyświetlanie wyników](./media/add-template-to-azure-pipelines/view-results.png)

Możesz wybrać aktualnie uruchomiony potok, aby wyświetlić szczegółowe informacje o zadaniach. Po zakończeniu zobaczysz wyniki dla każdego kroku.

## <a name="copy-and-deploy-tasks"></a>Kopiowanie i wdrażanie zadań

W tej sekcji pokazano, jak skonfigurować ciągłe wdrażanie przy użyciu dwóch zadań. Pierwsze zadanie etapuje artefakty do konta magazynu, a drugie zadanie wdraża szablon.

Aby skopiować pliki na konto magazynu, nazwa główna usługi dla połączenia usługi musi być przypisana jako współautor danych obiektów blob magazynu lub rolę właściciela danych obiektu blob magazynu. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą AzCopy](../../storage/common/storage-use-azcopy-v10.md).

W poniższym YAML przedstawiono [zadanie kopiowania plików platformy Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Istnieje kilka części tego zadania, które można poprawić w danym środowisku. `SourcePath`Wskazuje lokalizację artefaktów względem pliku potoku.

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

Po utworzeniu zadania Kopiuj plik można dodać zadanie w celu wdrożenia przygotowanego szablonu.

W poniższym YAML przedstawiono [zadanie wdrażania szablonu Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Istnieje kilka części tego zadania, które można przejrzeć bardziej szczegółowo.

- `deploymentScope`: Wybierz zakres wdrożenia z opcji: `Management Group` , `Subscription` i `Resource Group` . Aby dowiedzieć się więcej o zakresach, zobacz sekcję [Deployment Scopes](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Podaj nazwę utworzonego połączenia z usługą.

- `subscriptionId`: Podaj identyfikator subskrypcji docelowej. Ta właściwość dotyczy tylko zakresu wdrożenia grupy zasobów i zakresu wdrożenia subskrypcji.

- `resourceGroupName` i `location` : Podaj nazwę i lokalizację grupy zasobów, w której chcesz wdrożyć. Zadanie tworzy grupę zasobów, jeśli nie istnieje.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Podaj link do przygotowanego szablonu. Podczas ustawiania wartości należy używać zmiennych zwracanych z zadania kopiowania plików. Poniższy przykład łączy do szablonu o nazwie mainTemplate.json. Folder o nazwie **templates** jest uwzględniany, ponieważ w przypadku którego zadanie kopiowania pliku skopiował plik. W potoku podaj ścieżkę do szablonu i nazwę szablonu.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

Twój potok wygląda następująco:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Po wybraniu opcji **Zapisz** potok kompilacji zostanie automatycznie uruchomiony. Wróć do podsumowania potoku kompilacji i obejrzyj stan.

## <a name="next-steps"></a>Następne kroki

* Aby użyć operacji działania warunkowego w potoku, zobacz sekcję [testowanie szablonów ARM z What-If w potoku](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
* Aby dowiedzieć się więcej o korzystaniu z szablonów usługi ARM z akcjami usługi GitHub, zobacz [wdrażanie szablonów Azure Resource Manager przy użyciu akcji usługi GitHub](deploy-github-actions.md).
