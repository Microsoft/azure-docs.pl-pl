---
title: Tworzenie & wdrażanie rozwiązań HPC przy użyciu Azure Pipelines
description: Dowiedz się, jak wdrożyć potok kompilacji/wydania dla aplikacji HPC działającej na Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: 4d758d4613f68450be9c444063d3a6188d1aa689
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337580"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Używanie Azure Pipelines do kompilowania i wdrażania rozwiązań HPC

Azure DevOps Services zapewnić szereg narzędzi używanych przez zespoły programistyczne podczas kompilowania aplikacji niestandardowej. Narzędzia udostępniane przez usługę Azure DevOps mogą przetłumaczyć automatyczne Kompilowanie i testowanie rozwiązań obliczeniowych o wysokiej wydajności. W tym artykule pokazano, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie (CD) przy użyciu Azure Pipelines do rozwiązania obliczeniowego o wysokiej wydajności wdrożonego w Azure Batch.

Azure Pipelines zapewnia szeroki zakres nowoczesnych procesów ciągłej integracji/ciągłego wdrażania oraz umożliwia tworzenie, wdrażanie, testowanie i monitorowanie oprogramowania. Procesy te przyspieszają dostarczanie oprogramowania, co pozwala skupić się na kodzie, a nie z obsługą infrastruktury i operacji.

## <a name="create-an-azure-pipeline"></a>Tworzenie potoku platformy Azure

W tym przykładzie utworzymy potok kompilacji i wydania w celu wdrożenia infrastruktury Azure Batch i wydania pakietu aplikacji. Przy założeniu, że kod jest opracowywany lokalnie, jest to ogólny przepływ wdrażania:

![Diagram przedstawiający przepływ wdrożenia w naszym potoku](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Konfigurowanie

Aby wykonać kroki opisane w tym artykule, potrzebna jest organizacja usługi Azure DevOps i projekt zespołowy.

* [Tworzenie organizacji usługi Azure DevOps](/azure/devops/organizations/accounts/create-organization)
* [Tworzenie projektu na platformie Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Kontrola źródła dla danego środowiska

Kontrola źródła umożliwia zespołom śledzenie zmian wprowadzonych w bazie kodu i sprawdzanie poprzednich wersji kodu.

Zwykle kontrola źródła jest uważana za ręką z kodem oprogramowania. Jak działa podstawowa infrastruktura? Pozwala to na infrastrukturę jako kod, w której będziemy używać szablonów Azure Resource Manager lub innych alternatyw dla rozwiązań open source, aby deklaratywnie definiować podstawową infrastrukturę.

Ten przykład intensywnie korzysta z wielu Menedżer zasobów szablonów (dokumentów JSON) i istniejących plików binarnych. Możesz skopiować te przykłady do repozytorium i wypchnąć je do usługi Azure DevOps.

Struktura codebase użyta w tym przykładzie jest podobna do następującej:

* Folder **ARM-templates** zawierający kilka Azure Resource Manager szablonów. Te szablony zostały omówione w tym artykule.
* Folder **aplikacji klienta** , który jest kopią [Azure Batch przetwarzania plików platformy .NET z](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) przykładem narzędzia FFmpeg. Ten artykuł nie jest wymagany.
* Folder **HPC-aplikacja** , który jest wersją systemu Windows 64-bitową [Narzędzia FFmpeg 4.3.1](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-4.3.1-2020-10-01-essentials_build.7z).
* Folder **potoków** . Zawiera plik YAML, który tworzy konspekt procesu kompilacji. Ten artykuł został omówiony w artykule.

W tej sekcji założono, że znasz kontrolę wersji i projektujesz szablony Menedżer zasobów. Jeśli nie znasz tych pojęć, zobacz następujące strony, aby uzyskać więcej informacji.

* [Co to jest kontrola źródła?](/azure/devops/user-guide/source-control)
* [Zrozumienie struktury i składni szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Ten przykład wykorzystuje wiele szablonów Menedżer zasobów do wdrożenia naszego rozwiązania. W tym celu używamy wielu szablonów możliwości (podobnie jak w przypadku jednostek lub modułów), które implementują określoną część funkcjonalności. Używamy również szablonu kompleksowego rozwiązania, który jest odpowiedzialny za łączenie tych podstawowych funkcji. Istnieje kilka zalet tego podejścia:

* Podstawowymi szablonami możliwości mogą być pojedyncze testy jednostkowe.
* Podstawowe szablony możliwości można definiować jako standardowe wewnątrz organizacji i być ponownie używane w wielu rozwiązaniach.

W tym przykładzie istnieje szablon kompleksowego rozwiązania (deployment.json), który wdraża trzy szablony. Podstawowe szablony są szablonami możliwości odpowiedzialnymi za wdrażanie określonego aspektu rozwiązania.

![Przykład struktury połączonego szablonu przy użyciu szablonów Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Pierwszy szablon, który będzie wyglądał dla konta usługi Azure Storage. Nasze rozwiązanie wymaga konta magazynu do wdrożenia aplikacji na naszym koncie w usłudze Batch. Podczas kompilowania szablonów Menedżer zasobów dla kont magazynu warto pamiętać o [Menedżer zasobów przewodniku odwołania do szablonów dla typów zasobów Microsoft. Storage](/azure/templates/microsoft.storage/allversions) .

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

Następnie sprawdzimy szablon konta Azure Batch. Konto Azure Batch działa jako platforma do uruchamiania wielu aplikacji w pulach (grupowanie maszyn). W przypadku tworzenia szablonów Menedżer zasobów dla kont usługi Batch warto pamiętać o [Menedżer zasobów przewodniku odwoływania szablonów dla Microsoft.Batch](/azure/templates/microsoft.batch/allversions) .

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

Następny szablon pokazuje przykład tworzenia puli Azure Batch (maszyny zaplecza do przetwarzania aplikacji). W przypadku tworzenia szablonów Menedżer zasobów dla pul kont usługi Batch warto pamiętać o [Menedżer zasobów przewodniku odwoływania szablonów dla Microsoft.Batch](/azure/templates/microsoft.batch/allversions) .

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

Na koniec mamy szablon, który działa podobnie do programu Orchestrator. Ten szablon jest odpowiedzialny za wdrażanie szablonów możliwości.

Więcej informacji na temat [tworzenia połączonych Azure Resource Manager szablonów](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) można znaleźć w osobnym artykule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
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

#### <a name="the-hpc-solution"></a>Rozwiązanie HPC

Infrastruktura i oprogramowanie można zdefiniować jako kod i współpracujące w tym samym repozytorium.

W przypadku tego rozwiązania narzędzia FFmpeg jest używany jako pakiet aplikacji. Pakiet narzędzia FFmpeg można pobrać [tutaj](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip).

![Przykładowa struktura repozytorium git](media/batch-ci-cd/git-repository.jpg)

To repozytorium zawiera cztery główne sekcje:

* Folder **ARM-templates** przechowujący naszą infrastrukturę jako kod
* Folder **HPC-aplikacja** zawierający pliki binarne dla narzędzia FFmpeg
* Folder **potoków** zawierający definicję dla naszego potoku kompilacji.
* **Opcjonalne** : folder **klient-aplikacja** , który będzie przechowywać kod dla aplikacji .NET. Nie używamy tego przykładu w przykładzie, ale w Twoim projekcie możesz chcieć wykonać przebiegi aplikacji wsadowej HPC za pośrednictwem aplikacji klienckiej.

> [!NOTE]
> Jest to tylko jeden przykład struktury do bazy kodu. Takie podejście służy do prezentowania tego, że aplikacja, infrastruktura i kod potoku są przechowywane w tym samym repozytorium.

Teraz, gdy kod źródłowy został skonfigurowany, możemy rozpocząć pierwszą kompilację.

## <a name="continuous-integration"></a>Ciągła integracja

[Azure Pipelines](/azure/devops/pipelines/get-started/), w Azure DevOps Services, pomaga zaimplementować potok kompilacji, testowania i wdrażania aplikacji.

Na tym etapie potoku testy są zwykle uruchamiane w celu weryfikacji kodu i tworzenia odpowiednich fragmentów oprogramowania. Liczba i typy testów oraz wszelkie dodatkowe zadania, które są uruchamiane, zależą od szerszej strategii kompilowania i wydawania.

## <a name="preparing-the-hpc-application"></a>Przygotowywanie aplikacji HPC

W tym przykładzie nastąpi skoncentrowanie na folderze **HPC-Application** . **HPC-aplikacja** folder to oprogramowanie narzędzia FFmpeg, które będzie uruchamiane z poziomu konta Azure Batch.

1. Przejdź do sekcji kompilacje Azure Pipelines w organizacji usługi Azure DevOps. Utwórz **Nowy potok**.

    ![Tworzenie nowego potoku kompilacji](media/batch-ci-cd/new-build-pipeline.jpg)

1. Do utworzenia potoku kompilacji są dostępne dwie opcje:

    a. [Przy użyciu projektanta wizualnego](/azure/devops/pipelines/get-started-designer). Aby użyć tego polecenia, kliknij pozycję "Użyj projektanta wizualizacji" na stronie **Nowy potok** .

    b. [Korzystanie z kompilacji YAML](/azure/devops/pipelines/get-started-yaml). Nowy potok YAML można utworzyć, klikając opcję Azure Repos lub GitHub na stronie nowe potoku. Możesz również zapisać Poniższy przykład w kontroli źródła i odwołać się do istniejącego pliku YAML, klikając w projektancie wizualizacji, a następnie używając szablonu YAML.

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

    ![Przykład istniejącego potoku kompilacji](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Wyświetl aktualizacje na żywo na postęp kompilacji na platformie Azure DevOps, przechodząc do sekcji **kompilacja** Azure Pipelines. Wybierz odpowiednią kompilację z definicji kompilacji.

    ![Wyświetlanie danych wyjściowych na żywo z kompilacji](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Jeśli używasz aplikacji klienckiej do wykonywania aplikacji wsadowej HPC, musisz utworzyć oddzielną definicję kompilacji dla tej aplikacji. W dokumentacji [Azure Pipelines](/azure/devops/pipelines/get-started/index) można znaleźć kilka przewodników związanych z założeniami.

## <a name="continuous-deployment"></a>Ciągłe wdrażanie

Azure Pipelines również używany do wdrażania aplikacji i podstawowej infrastruktury. [Potoki wersji](/azure/devops/pipelines/release) to składnik, który umożliwia ciągłe wdrażanie i automatyzuje proces tworzenia wersji.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Wdrażanie aplikacji i podstawowej infrastruktury

Istnieje kilka kroków związanych z wdrażaniem infrastruktury. W przypadku używania [połączonych szablonów](../azure-resource-manager/templates/linked-templates.md)te szablony będą musiały być dostępne z publicznego punktu końcowego (http lub https). Może to być repozytorium w serwisie GitHub lub konto usługi Azure Blob Storage lub inną lokalizację magazynu. Przekazane artefakty szablonu mogą pozostać bezpieczne, ponieważ mogą być przechowywane w trybie prywatnym, ale dostęp przy użyciu jakiejś postaci tokenu sygnatury dostępu współdzielonego (SAS). W poniższym przykładzie pokazano, jak wdrożyć infrastrukturę z szablonami z poziomu obiektu BLOB usługi Azure Storage.

1. Utwórz **nową definicję wydania** i wybierz pustą definicję. Następnie należy zmienić nazwę nowo utworzonego środowiska na coś istotnego dla naszego potoku.

    ![Początkowy potok wydania](media/batch-ci-cd/Release-0.jpg)

1. Utwórz zależność od potoku kompilacji, aby uzyskać dane wyjściowe dla naszej aplikacji HPC.

    > [!NOTE]
    > Po ponownym zapisaniu **aliasu źródłowego** należy zwrócić uwagę na to, że będzie to możliwe, gdy zadania zostaną utworzone w ramach definicji wydania.

    ![Utwórz łącze artefaktu do HPCApplicationPackage w odpowiednim potoku kompilacji](media/batch-ci-cd/Release-1.jpg)

1. Utwórz link do innego artefaktu, tym razem repozytorium platformy Azure. Jest to wymagane w celu uzyskania dostępu do szablonów Menedżer zasobów przechowywanych w repozytorium. Ponieważ szablony Menedżer zasobów nie wymagają kompilacji, nie trzeba wypchnąć ich za pomocą potoku kompilacji.

    > [!NOTE]
    > Po ponownym zapisaniu **aliasu źródłowego** należy zwrócić uwagę na to, że będzie to możliwe, gdy zadania zostaną utworzone w ramach definicji wydania.

    ![Utwórz łącze artefaktu do Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Przejdź do sekcji **zmienne** . Zaleca się utworzenie wielu zmiennych w potoku, dlatego nie można umieścić tych samych informacji w wielu zadaniach. Są to zmienne używane w tym przykładzie i wpływ na wdrożenie.

    * **applicationStorageAccountName** : nazwa konta magazynu do przechowywania plików binarnych aplikacji HPC
    * **batchAccountApplicationName** : Nazwa aplikacji na koncie Azure Batch
    * **batchAccountName** : nazwa konta Azure Batch
    * **batchAccountPoolName** : Nazwa puli maszyn wirtualnych przetwarzających przetwarzanie
    * **batchApplicationId** : unikatowy identyfikator aplikacji Azure Batch
    * **batchApplicationVersion** : wersja semantyczna aplikacji wsadowej (czyli pliki binarne narzędzia FFmpeg)
    * **Lokalizacja** : Lokalizacja zasobów platformy Azure, które mają zostać wdrożone
    * **resourceGroupName** : Nazwa grupy zasobów, która ma zostać utworzona, oraz miejsce, w której zostaną wdrożone zasoby
    * **storageAccountName** : nazwa konta magazynu do przechowywania połączonych Menedżer zasobów szablonów

    ![Przykład zmiennych ustawionych dla Azure Pipelines wydania](media/batch-ci-cd/Release-4.jpg)

1. Przejdź do zadań dla środowiska deweloperskiego. W poniższej migawce można zobaczyć sześć zadań. Te zadania spowodują: pobranie plików spakowanej narzędzia FFmpeg, wdrożenie konta magazynu na potrzeby hostowania zagnieżdżonych szablonów Menedżer zasobów, skopiowanie tych Menedżer zasobów szablonów do konta magazynu, wdrożenie konta programu Batch i wymaganych zależności, utworzenie aplikacji na koncie Azure Batch i przekazanie pakietu aplikacji do konta Azure Batch.

    ![Przykład zadań używanych do wydania aplikacji HPC w celu Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Dodaj zadanie **artefakt potoku pobierania (wersja zapoznawcza)** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Pobierz ApplicationPackage do agenta
    * **Nazwa artefaktu do pobrania:** HPC-Application
    * **Ścieżka do pobrania** : $ (System. DefaultWorkingDirectory)

1. Utwórz konto magazynu do przechowywania artefaktów. Można użyć istniejącego konta magazynu z rozwiązania, ale w przypadku samodzielnej próbki i izolacji zawartości firma Microsoft tworzy dedykowane konto magazynu dla naszych artefaktów (w tym Menedżer zasobów szablonów).

    Dodaj zadanie **wdrażania grupy zasobów platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Wdróż konto magazynu dla szablonów Menedżer zasobów
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Akcja** : Utwórz lub Zaktualizuj grupę zasobów
    * **Grupa zasobów** : $ (resourceGroupName)
    * **Lokalizacja** : $ (lokalizacja)
    * **Szablon** : $ (System. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /ARM-templates/storageAccount.json
    * **Przesłoń parametry szablonu** :-AccountName $ (storageAccountName)

1. Przekaż artefakty z kontroli źródła do konta magazynu. Istnieje zadanie potoku platformy Azure do wykonania tej akcji. W ramach tego zadania, adres URL kontenera konta magazynu i token sygnatury dostępu współdzielonego można wypróbować do zmiennej w Azure Pipelines. Oznacza to, że można go ponownie wykorzystać w ramach tej fazy agenta.

    Dodaj zadanie **kopiowania plików platformy Azure** i ustaw następujące właściwości:
    * **Źródło:** $ (System. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /ARM-templates/
    * **Typ połączenia platformy Azure** : Azure Resource Manager
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Typ docelowy** : obiekt blob platformy Azure
    * **Konto magazynu RM** : $ (storageAccountName)
    * **Nazwa kontenera** : szablony
    * **Identyfikator URI kontenera magazynu** : templateContainerUri
    * **Token sygnatury dostępu współdzielonego kontenera magazynu** : templateContainerSasToken

1. Wdróż szablon programu Orchestrator. Odwołaj szablon programu Orchestrator ze starszej wersji, można zauważyć, że jako uzupełnienie tokenu sygnatury dostępu współdzielone są parametry dla adresu URL kontenera konta magazynu. Należy zauważyć, że zmienne wymagane w szablonie Menedżer zasobów są przechowywane w sekcji zmienne definicji wydania lub zostały ustawione z innego zadania Azure Pipelines (na przykład część zadania kopiowania obiektów blob platformy Azure).

    Dodaj zadanie **wdrażania grupy zasobów platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Wdróż Azure Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Akcja** : Utwórz lub Zaktualizuj grupę zasobów
    * **Grupa zasobów** : $ (resourceGroupName)
    * **Lokalizacja** : $ (lokalizacja)
    * **Szablon** : $ (System. ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /ARM-templates/deployment.json
    * **Przesłoń parametry szablonu** : ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Typowym sposobem jest użycie zadań Azure Key Vault. Jeśli nazwa główna usługi (połączenie z subskrypcją platformy Azure) ma odpowiednie zasady dostępu, można pobrać klucze tajne z Azure Key Vault i używać ich jako zmiennych w potoku. Nazwa wpisu tajnego zostanie ustawiona z skojarzoną wartością. Na przykład wpis tajny sshPassword może być przywoływany przy użyciu $ (sshPassword) w definicji wydania.

1. Następne kroki wywołują interfejs wiersza polecenia platformy Azure. Pierwszy służy do tworzenia aplikacji w Azure Batch. i przekaż skojarzone pakiety.

    Dodaj zadanie **interfejsu wiersza polecenia platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Tworzenie aplikacji na koncie Azure Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Lokalizacja skryptu** : skrypt wbudowany
    * **Skrypt wbudowany** : ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Drugi krok służy do przekazywania skojarzonych pakietów do aplikacji. W naszym przypadku pliki narzędzia FFmpeg.

    Dodaj zadanie **interfejsu wiersza polecenia platformy Azure** i ustaw następujące właściwości:
    * **Nazwa wyświetlana:** Przekaż pakiet do konta Azure Batch
    * **Subskrypcja platformy Azure:** Wybierz odpowiednią subskrypcję platformy Azure
    * **Lokalizacja skryptu** : skrypt wbudowany
    * **Skrypt wbudowany** : ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Numer wersji pakietu aplikacji jest ustawiony na zmienną. Jest to wygodne, Jeśli zastępowanie poprzednich wersji pakietu działa prawidłowo, a jeśli chcesz ręcznie kontrolować numer wersji pakietu wypchnięcia do Azure Batch.

1. Utwórz nową wersję, wybierając pozycję **release > utworzyć nową wersję**. Po wyzwoleniu wybierz łącze do nowej wersji, aby wyświetlić stan.

1. Możesz wyświetlić dane wyjściowe z agenta, wybierając przycisk **dzienniki** poniżej swojego środowiska.

    ![Wyświetlanie stanu wydania](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testowanie środowiska

Po skonfigurowaniu środowiska upewnij się, że następujące testy mogą zostać wykonane pomyślnie.

Połącz się z nowym kontem Azure Batch przy użyciu interfejsu wiersza polecenia platformy Azure z wiersza poleceń programu PowerShell.

* Zaloguj się do konta platformy Azure przy użyciu `az login` i postępuj zgodnie z instrukcjami dotyczącymi uwierzytelniania.
* Teraz uwierzytelniaj konto w usłudze Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

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

Oprócz tego artykułu istnieją dwa samouczki korzystające z narzędzia FFmpeg przy użyciu platformy .NET i języka Python. Zobacz te samouczki, aby uzyskać więcej informacji na temat korzystania z konta usługi Batch za pośrednictwem prostej aplikacji.

* [uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API Python](tutorial-parallel-python.md)
* [Uruchamianie równoległego obciążenia w usłudze Azure Batch przy użyciu interfejsu API środowiska .NET](tutorial-parallel-dotnet.md)
