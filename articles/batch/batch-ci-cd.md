---
title: Tworzenie & wdrażanie rozwiązań HPC przy użyciu Azure Pipelines
description: Dowiedz się, jak wdrożyć potok kompilacji/wydania dla aplikacji HPC działającej na Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435549"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Używanie Azure Pipelines do kompilowania i wdrażania rozwiązań HPC

Narzędzia udostępniane przez usługę Azure DevOps mogą przetłumaczyć automatyczne Kompilowanie i testowanie rozwiązań obliczeniowych o wysokiej wydajności (HPC). [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) udostępnia wiele procesów nowoczesnej integracji ciągłej (ci) i ciągłego wdrażania (CD) do kompilowania, wdrażania, testowania i monitorowania oprogramowania. Procesy te przyspieszają dostarczanie oprogramowania, co pozwala skupić się na kodzie, a nie z obsługą infrastruktury i operacji.

W tym artykule wyjaśniono, jak skonfigurować procesy ciągłej integracji/ciągłego wdrażania za pomocą [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) dla rozwiązań HPC wdrożonych na Azure Batch.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebna jest [organizacja usługi Azure DevOps](/azure/devops/organizations/accounts/create-organization). Należy również [utworzyć projekt w usłudze Azure DevOps](/azure/devops/organizations/projects/create-project).

Przed rozpoczęciem warto uzyskać podstawową wiedzę na temat [kontroli źródła](/azure/devops/user-guide/source-control) i [składni szablonu Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) .

## <a name="create-an-azure-pipeline"></a>Tworzenie potoku platformy Azure

W tym przykładzie utworzysz potok kompilacja i wydanie, aby wdrożyć infrastrukturę Azure Batch i wydać pakiet aplikacji. Przy założeniu, że kod jest opracowywany lokalnie, jest to ogólny przepływ wdrażania:

![Diagram przedstawiający przepływ wdrożenia w potoku,](media/batch-ci-cd/DeploymentFlow.png)

Ten przykład używa kilku Azure Resource Manager szablonów i istniejących plików binarnych. Możesz skopiować te przykłady do repozytorium i wypchnąć je do usługi Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Opis Azure Resource Manager szablonów

W tym przykładzie zastosowano kilka Azure Resource Manager szablonów do wdrożenia rozwiązania. Trzy szablony możliwości (podobne do jednostek lub modułów) służą do implementowania konkretnej funkcji. Kompleksowy szablon rozwiązania (deployment.json) służy do wdrażania tych podstawowych szablonów możliwości. Ta [Struktura połączonego szablonu ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) umożliwia przetestowanie i użycie poszczególnych szablonów możliwości osobno w różnych rozwiązaniach.

![Diagram przedstawiający strukturę połączonego szablonu przy użyciu szablonów Azure Resource Manager.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Ten szablon definiuje konto magazynu platformy Azure, które jest wymagane do wdrożenia aplikacji na koncie usługi Batch. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik dotyczący szablonu Menedżer zasobów dla typów zasobów Microsoft. Storage](/azure/templates/microsoft.storage/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Następny szablon definiuje [konto Azure Batch](accounts.md). Konto wsadowe działa jako platforma do uruchamiania wielu aplikacji w ramach [pul](nodes-and-pools.md#pools). Aby uzyskać szczegółowe informacje, zobacz [Przewodnik dotyczący szablonu Menedżer zasobów dla typów zasobów Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Następny szablon tworzy pulę wsadową na koncie wsadowym. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik dotyczący szablonu Menedżer zasobów dla typów zasobów Microsoft.Batch](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Ostatni szablon działa jako koordynator, wdrażając trzy podstawowe szablony możliwości.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>Informacje o rozwiązaniu HPC

Jak wspomniano wcześniej, w tym przykładzie zastosowano kilka Azure Resource Manager szablonów i istniejących plików binarnych. Możesz skopiować te przykłady do repozytorium i wypchnąć je do usługi Azure DevOps.

W przypadku tego rozwiązania narzędzia FFmpeg jest używany jako pakiet aplikacji. [Pakiet narzędzia FFmpeg można pobrać,](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) jeśli go jeszcze nie masz.

![Zrzut ekranu struktury repozytorium.](media/batch-ci-cd/git-repository.jpg)

To repozytorium zawiera cztery główne sekcje:

- Folder **"ARM-templates"** zawierający szablony Azure Resource Manager
- Folder **HPC-aplikacja** zawierający wersję systemu Windows 64-bitową [Narzędzia FFmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- Folder **potoków** zawierający plik YAML, który definiuje proces potoku kompilacji.
- Opcjonalnie: folder **aplikacji klienta** , który jest kopią [Azure Batch przetwarzania plików platformy .NET z](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) przykładem narzędzia FFmpeg. Ta aplikacja nie jest wymagana w tym artykule.


> [!NOTE]
> Jest to tylko jeden przykład struktury do bazy kodu. Takie podejście służy do prezentowania tego, że aplikacja, infrastruktura i kod potoku są przechowywane w tym samym repozytorium.

Teraz, gdy kod źródłowy został skonfigurowany, można rozpocząć pierwszą kompilację.

## <a name="continuous-integration"></a>Ciągła integracja

[Azure Pipelines](/azure/devops/pipelines/get-started/), w Azure DevOps Services, pomaga zaimplementować potok kompilacji, testowania i wdrażania aplikacji.

Na tym etapie potoku testy są zwykle uruchamiane w celu weryfikacji kodu i tworzenia odpowiednich fragmentów oprogramowania. Liczba i typy testów oraz wszelkie dodatkowe zadania, które są uruchamiane, zależą od szerszej strategii kompilowania i wydawania.

## <a name="prepare-the-hpc-application"></a>Przygotowywanie aplikacji HPC

W tej sekcji nastąpi współpraca z folderem **HPC-Application** . Ten folder zawiera oprogramowanie (narzędzia FFmpeg), które będzie działać w ramach konta Azure Batch.

1. Przejdź do sekcji kompilacje Azure Pipelines w organizacji usługi Azure DevOps. Utwórz **Nowy potok**.

    ![Zrzut ekranu przedstawiający nowy ekran potoku.](media/batch-ci-cd/new-build-pipeline.jpg)

1. Do utworzenia potoku kompilacji są dostępne dwie opcje:

    a. [Użyj projektanta wizualnego](/azure/devops/pipelines/get-started-designer). Aby to zrobić, wybierz pozycję "Użyj projektanta wizualizacji" na **nowej stronie potoku** .

    b. [Użyj kompilacji YAML](/azure/devops/pipelines/get-started-yaml). Nowy potok YAML można utworzyć, klikając opcję Azure Repos lub GitHub na stronie **nowe potoku** . Możesz również przechowywać Poniższy przykład w kontroli źródła i odwołać się do istniejącego pliku YAML, wybierając pozycję Visual Designer, a następnie używając szablonu YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Gdy kompilacja zostanie skonfigurowana zgodnie z wymaganiami, wybierz pozycję **zapisz & kolejkę**. Jeśli włączono ciągłą integrację (w sekcji **wyzwalacze** ), kompilacja zostanie automatycznie wyzwolona, gdy zostanie wykonane nowe zatwierdzenie do repozytorium, spełniając warunki ustawione w kompilacji.

    ![Zrzut ekranu istniejącego potoku kompilacji.](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Wyświetl aktualizacje na żywo na postęp kompilacji na platformie Azure DevOps, przechodząc do sekcji **kompilacja** Azure Pipelines. Wybierz odpowiednią kompilację z definicji kompilacji.

    ![Zrzut ekranu przedstawiający dane wyjściowe z kompilacji na żywo w usłudze Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Jeśli używasz aplikacji klienckiej do wykonywania rozwiązania HPC, musisz utworzyć oddzielną definicję kompilacji dla tej aplikacji. W dokumentacji [Azure Pipelines](/azure/devops/pipelines/get-started/index) można znaleźć kilka przewodników związanych z założeniami.

## <a name="continuous-deployment"></a>Ciągłe wdrażanie

Azure Pipelines jest również używany do wdrażania aplikacji i podstawowej infrastruktury. [Potoki wydań](/azure/devops/pipelines/release) umożliwiają ciągłe wdrażanie i automatyzuje proces tworzenia wersji.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Wdrażanie aplikacji i podstawowej infrastruktury

Istnieje kilka kroków związanych z wdrażaniem infrastruktury. Ponieważ to rozwiązanie używa [połączonych szablonów](../azure-resource-manager/templates/linked-templates.md), te szablony będą musiały być dostępne z publicznego punktu końcowego (http lub https). Może to być repozytorium w serwisie GitHub lub konto usługi Azure Blob Storage lub inną lokalizację magazynu. Przekazane artefakty szablonu mogą pozostać bezpieczne, ponieważ mogą być przechowywane w trybie prywatnym, ale dostęp przy użyciu jakiejś postaci tokenu sygnatury dostępu współdzielonego (SAS).

W poniższym przykładzie pokazano, jak wdrożyć infrastrukturę z szablonami z poziomu obiektu BLOB usługi Azure Storage.

1. Utwórz **nową definicję wydania**, a następnie wybierz pustą definicję. Zmień nazwę nowo utworzonego środowiska na coś istotnego dla potoku.

    ![Zrzut ekranu początkowego potoku wydania.](media/batch-ci-cd/Release-0.jpg)

1. Utwórz zależność od potoku kompilacji, aby uzyskać dane wyjściowe dla aplikacji HPC.

    > [!NOTE]
    > Zwróć uwagę na **alias źródłowy**, ponieważ będzie on konieczny, gdy zadania zostaną utworzone w ramach definicji wydania.

    ![Zrzut ekranu przedstawiający łącze artefaktu do HPCApplicationPackage w odpowiednim potoku kompilacji.](media/batch-ci-cd/Release-1.jpg)

1. Utwórz link do innego artefaktu, tym razem repozytorium platformy Azure. Jest to wymagane w celu uzyskania dostępu do szablonów Menedżer zasobów przechowywanych w repozytorium. Ponieważ szablony Menedżer zasobów nie wymagają kompilacji, nie trzeba wypchnąć ich za pomocą potoku kompilacji.

    > [!NOTE]
    > Ponownie Zanotuj **alias źródłowy**, ponieważ będzie on potrzebny później.

    ![Zrzut ekranu przedstawiający łącze artefaktu do Azure Repos.](media/batch-ci-cd/Release-2.jpg)

1. Przejdź do sekcji **zmienne** . W potoku trzeba utworzyć wiele zmiennych, aby nie trzeba było ponownie wprowadzać tych samych informacji do wielu zadań. W tym przykładzie zastosowano następujące zmienne:

   - **applicationStorageAccountName**: nazwa konta magazynu, w którym znajdują się pliki binarne aplikacji HPC
   - **batchAccountApplicationName**: Nazwa aplikacji na koncie wsadowym
   - **batchAccountName**: nazwa konta wsadowego
   - **batchAccountPoolName**: Nazwa puli maszyn wirtualnych przetwarzających przetwarzanie
   - **batchApplicationId**: unikatowy identyfikator aplikacji usługi Batch
   - **batchApplicationVersion**: wersja semantyczna aplikacji wsadowej (czyli pliki binarne narzędzia FFmpeg)
   - **Lokalizacja**: Lokalizacja zasobów platformy Azure, które mają zostać wdrożone
   - **resourceGroupName**: Nazwa grupy zasobów, która ma zostać utworzona, oraz miejsce, w której zostaną wdrożone zasoby
   - **storageAccountName**: nazwa konta magazynu z połączonymi szablonami Menedżer zasobów

   ![Zrzut ekranu przedstawiający zmienne ustawione dla Azure Pipelines wydania.](media/batch-ci-cd/Release-4.jpg)

1. Przejdź do zadań dla środowiska deweloperskiego. W poniższej migawce można zobaczyć sześć zadań. Te zadania spowodują: pobranie plików spakowanej narzędzia FFmpeg, wdrożenie konta magazynu na potrzeby hostowania zagnieżdżonych szablonów Menedżer zasobów, skopiowanie tych Menedżer zasobów szablonów do konta magazynu, wdrożenie konta programu Batch i wymaganych zależności, utworzenie aplikacji na koncie Azure Batch i przekazanie pakietu aplikacji do konta Azure Batch.

    ![Zrzut ekranu przedstawiający zadania używane do wydania aplikacji HPC w celu Azure Batch.](media/batch-ci-cd/Release-3.jpg)

1. Dodaj zadanie **artefakt potoku pobierania (wersja zapoznawcza)** i ustaw następujące właściwości:
    - **Nazwa wyświetlana:** Pobierz ApplicationPackage do agenta
    - **Nazwa artefaktu do pobrania:** HPC-Application
    - **Ścieżka do pobrania**: $ (System. DefaultWorkingDirectory)

1. Utwórz konto magazynu do przechowywania szablonów Azure Resource Manager. Można użyć istniejącego konta magazynu z rozwiązania, ale w celu obsługi tego samodzielnego przykładu i wyodrębnienia zawartości można utworzyć dedykowane konto magazynu.

    Dodaj zadanie **wdrażania grupy zasobów platformy Azure** i ustaw następujące właściwości:
    - **Nazwa wyświetlana:** Wdróż konto magazynu dla szablonów Menedżer zasobów
    - **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    - **Akcja**: Utwórz lub Zaktualizuj grupę zasobów
    - **Grupa zasobów**: $ (resourceGroupName)
    - **Lokalizacja**: $ (lokalizacja)
    - **Szablon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-templates/storageAccount.json
    - **Przesłoń parametry szablonu**:-AccountName $ (storageAccountName)

1. Przekaż artefakty z kontroli źródła do konta magazynu przy użyciu Azure Pipelines. W ramach tego zadania Azure Pipelines można przetworzyć identyfikator URI kontenera konta magazynu i token sygnatury dostępu współdzielonego do zmiennej w Azure Pipelines, umożliwiając ich użycie w całej fazie agenta.

    Dodaj zadanie **kopiowania plików platformy Azure** i ustaw następujące właściwości:
    - **Źródło:** $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-templates/
    - **Typ połączenia platformy Azure**: Azure Resource Manager
    - **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    - **Typ docelowy**: obiekt blob platformy Azure
    - **Konto magazynu RM**: $ (storageAccountName)
    - **Nazwa kontenera**: szablony
    - **Identyfikator URI kontenera magazynu**: templateContainerUri
    - **Token sygnatury dostępu współdzielonego kontenera magazynu**: templateContainerSasToken

1. Wdróż szablon programu Orchestrator. Ten szablon zawiera parametry dla identyfikatora URI kontenera konta magazynu i tokenu SAS. Zmienne wymagane w szablonie Menedżer zasobów są przechowywane w sekcji zmienne definicji wydania lub zostały ustawione na podstawie innego zadania Azure Pipelines (na przykład część zadania kopiowania obiektów blob platformy Azure).

    Dodaj zadanie **wdrażania grupy zasobów platformy Azure** i ustaw następujące właściwości:
    - **Nazwa wyświetlana:** Wdróż Azure Batch
    - **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    - **Akcja**: Utwórz lub Zaktualizuj grupę zasobów
    - **Grupa zasobów**: $ (resourceGroupName)
    - **Lokalizacja**: $ (lokalizacja)
    - **Szablon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-templates/deployment.json
    - **Przesłoń parametry szablonu**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Typowym sposobem jest użycie zadań Azure Key Vault. Jeśli jednostka usługi połączona z subskrypcją platformy Azure ma odpowiednie zasady dostępu, może pobrać klucze tajne z Azure Key Vault i używać ich jako zmiennych w potoku. Nazwa wpisu tajnego zostanie ustawiona z skojarzoną wartością. Na przykład wpis tajny sshPassword może być przywoływany przy użyciu $ (sshPassword) w definicji wydania.

1. Następne kroki wywołują interfejs wiersza polecenia platformy Azure. Pierwszy służy do tworzenia aplikacji w Azure Batch i przekazywania skojarzonych pakietów.

    Dodaj zadanie **interfejsu wiersza polecenia platformy Azure** i ustaw następujące właściwości:
    - **Nazwa wyświetlana:** Tworzenie aplikacji na koncie Azure Batch
    - **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    - **Lokalizacja skryptu**: skrypt wbudowany
    - **Skrypt wbudowany**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. Drugi krok służy do przekazywania skojarzonych pakietów do aplikacji (w tym przypadku pliki narzędzia FFmpeg).

    Dodaj zadanie **interfejsu wiersza polecenia platformy Azure** i ustaw następujące właściwości:
    - **Nazwa wyświetlana:** Przekaż pakiet do konta Azure Batch
    - **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    - **Lokalizacja skryptu**: skrypt wbudowany
    - **Skrypt wbudowany**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > Numer wersji pakietu aplikacji jest ustawiony na zmienną. Pozwala to zastąpić poprzednie wersje pakietu i pozwala ręcznie kontrolować numer wersji pakietu wypychanego do Azure Batch.

1. Utwórz nową wersję, wybierając pozycję **release > utworzyć nową wersję**. Po wyzwoleniu wybierz łącze do nowej wersji, aby wyświetlić stan.

1. Wyświetl dane wyjściowe z agenta, wybierając przycisk **dzienniki** poniżej swojego środowiska.

    ![Zrzut ekranu przedstawiający stan wydania.](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Testowanie środowiska

Po skonfigurowaniu środowiska upewnij się, że następujące testy mogą zostać wykonane pomyślnie.

Połącz się z nowym kontem Azure Batch przy użyciu interfejsu wiersza polecenia platformy Azure z wiersza poleceń programu PowerShell.

- Zaloguj się do konta platformy Azure przy użyciu `az login` i postępuj zgodnie z instrukcjami dotyczącymi uwierzytelniania.
- Teraz uwierzytelniaj konto w usłudze Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Wyświetl listę dostępnych aplikacji

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Sprawdź, czy pula jest prawidłowa

```azurecli
az batch pool list
```

Zwróć uwagę na wartość `currentDedicatedNodes` z danych wyjściowych tego polecenia. Ta wartość jest dostosowywana w następnym teście.

#### <a name="resize-the-pool"></a>Zmień rozmiar puli

Zmień rozmiar puli, tak aby można było korzystać z węzłów obliczeniowych na potrzeby testowania zadań i zadań, zaznacz polecenie z listą pul, aby wyświetlić bieżący stan do momentu zakończenia zmiany rozmiaru i dostępnych węzłów

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Następne kroki

Zobacz te samouczki, aby dowiedzieć się, jak korzystać z konta wsadowego za pośrednictwem prostej aplikacji.

- [uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API Python](tutorial-parallel-python.md)
- [Uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API środowiska .NET](tutorial-parallel-dotnet.md)
