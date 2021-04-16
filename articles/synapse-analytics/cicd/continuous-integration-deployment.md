---
title: Ciągła integracja i ciągłe dostarczanie dla obszaru roboczego usługi Synapse
description: Dowiedz się, jak za pomocą ciągłej integracji i dostarczania wdrażać zmiany w obszarze roboczym z jednego środowiska (testowania, testowania, produkcji) do innego.
author: liud
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5f68e3698f8616b581d319bc19d2a8c636c79c36
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566090"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Ciągła integracja i ciągłe dostarczanie dla Azure Synapse roboczego

## <a name="overview"></a>Omówienie

Ciągła integracja (CI) to proces automatyzowania kompilowania i testowania kodu za każdym razem, gdy członek zespołu zatwierdza zmiany w kontroli wersji. Ciągłe wdrażanie (CD) to proces kompilowania, testowania, konfigurowania i wdrażania z wielu środowisk testowych lub przejściowych do środowiska produkcyjnego.

W Azure Synapse obszar roboczy ciągła integracja i ciągłe dostarczanie (CI/CD) przenosi wszystkie jednostki z jednego środowiska (testowania, testowania, produkcji) do innego. Aby promować obszar roboczy do innego obszaru roboczego, istnieją dwie części: użycie szablonów Azure Resource Manager [do](../../azure-resource-manager/templates/overview.md) tworzenia lub aktualizowania zasobów obszaru roboczego (pul i obszarów roboczych); migrowanie artefaktów (skryptów SQL, notesu, definicji zadania platformy Spark, potoków, zestawów danych, przepływów danych itp.) za pomocą narzędzi usługi Synapse CI/CD Azure DevOps. 

W tym artykule przedstawiono użycie potoku wydania platformy Azure w celu zautomatyzowania wdrażania obszaru roboczego usługi Synapse w wielu środowiskach.

## <a name="prerequisites"></a>Wymagania wstępne

-   Obszar roboczy używany do tworzenia aplikacji został skonfigurowany za pomocą repozytorium Git w programie Studio. Zobacz Kontrola źródła w [Synapse Studio](source-control.md).
-   Projekt Azure DevOps został przygotowany do uruchomienia potoku wydania.

## <a name="set-up-a-release-pipelines"></a>Konfigurowanie potoków wydania

1.  W [Azure DevOps](https://dev.azure.com/)otwórz projekt utworzony dla wydania.

1.  W lewej części strony wybierz pozycję **Potoki**, a następnie wybierz **pozycję Wydania**.

    ![Wybieranie opcji Potoki, Wydania](media/create-release-1.png)

1.  Wybierz **pozycję Nowy potok** lub, jeśli masz istniejące potoki, wybierz pozycję **Nowy,** a następnie pozycję Nowy potok **wydania.**

1.  Wybierz szablon **Pusty** zadanie.

    ![Wybierz pozycję Puste zadanie](media/create-release-select-empty.png)

1.  W polu **Nazwa etapu** wprowadź nazwę środowiska.

1.  Wybierz **pozycję Add artifact**(Dodaj artefakt), a następnie wybierz repozytorium git skonfigurowane przy użyciu Synapse Studio. Wybierz repozytorium git używane do zarządzania szablonem ARM pul i obszarów roboczych. Jeśli używasz usługi GitHub jako źródła, musisz utworzyć połączenie z usługą dla konta usługi GitHub i repozytoria ściągania. Aby uzyskać więcej informacji na temat [połączenia z usługą](/azure/devops/pipelines/library/service-endpoints) 

    ![Dodawanie gałęzi publikowania](media/release-creation-github.png)

1.  Wybierz gałąź szablonu arm dla aktualizacji zasobów. W przypadku **wersji domyślnej** wybierz pozycję **Najnowszy z gałęzi domyślnej**.

    ![Dodawanie szablonu usługi ARM](media/release-creation-arm-branch.png)

1.  Wybierz [gałąź publikowania](source-control.md#configure-publishing-settings) repozytorium dla gałęzi **Domyślne.** Domyślnie ta gałąź publikowania to `workspace_publish` . W przypadku **wersji domyślnej** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodawanie artefaktu](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Konfigurowanie zadania etapowego do tworzenia i aktualizowania zasobów usługi ARM 

Dodaj zadanie Azure Resource Manager Wdrażania, aby utworzyć lub zaktualizować zasoby, w tym obszar roboczy i pule:

1. W widoku etapu wybierz pozycję **Wyświetl zadania etapu.**

    ![Widok etapu](media/release-creation-stage-view.png)

1. Utwórz nowe zadanie. Wyszukaj pozycję **Wdrożenie szablonu usługi ARM,** a następnie wybierz **pozycję Dodaj.**

1. W zadaniu Wdrożenie wybierz subskrypcję, grupę zasobów i lokalizację docelowego obszaru roboczego. W razie potrzeby podaj poświadczenia.

1. Na liście **Akcja** wybierz pozycję **Utwórz lub zaktualizuj grupę zasobów.**

1. Wybierz przycisk wielokropka (**...**) obok **pola** Szablon. Wyszukiwanie szablonu Azure Resource Manager docelowego obszaru roboczego

1. Wybierz **pozycję ...** obok pola **Parametry szablonu,** aby wybrać plik parametrów.

1. Wybierz **pozycję ...** obok pola **Zastąp parametry szablonu** i wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

1. Wybierz **opcję Przyrostowe** dla **opcji Tryb wdrażania.**
    
    ![wdrażanie obszaru roboczego i pul](media/pools-resource-deploy.png)

1. (Opcjonalnie) Dodaj **Azure PowerShell** przypisania roli obszaru roboczego przyznawania i aktualizowania. Jeśli używasz potoku wydania do utworzenia obszaru roboczego usługi Synapse, jednostkę usługi potoku dodawana jest jako domyślny administrator obszaru roboczego. Możesz uruchomić program PowerShell, aby udzielić innym kontom dostępu do obszaru roboczego. 
    
    ![udzielanie uprawnień](media/release-creation-grant-permission.png)

 > [!WARNING]
> W trybie kompletnego wdrażania zasoby, które istnieją w grupie zasobów, ale nie są określone w nowym Resource Manager zostaną **usunięte.** Aby uzyskać więcej informacji, zapoznaj się z [Azure Resource Manager wdrożenia](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Konfigurowanie zadania etapowego dla wdrożenia artefaktów 

Użyj rozszerzenia wdrażania obszaru roboczego usługi [Synapse,](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) aby wdrożyć inne elementy w obszarze roboczym usługi Synapse, takie jak zestaw danych, skrypt SQL, notes, definicja zadania platformy Spark, przepływ danych, potok, połączona usługa, poświadczenia i Integration Runtime.  

1. Wyszukaj i pobierz rozszerzenie z witryny **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Uzyskiwanie rozszerzenia](media/get-extension-from-market.png)

1. Wybierz organizację do zainstalowania rozszerzenia. 

     ![Instalowanie rozszerzenia](media/install-extension.png)

1. Upewnij się Azure DevOps że dla jednostki usługi potoku udzielono uprawnień subskrypcji, a także przypisano go jako administratora obszaru roboczego dla docelowego obszaru roboczego. 

1. Utwórz nowe zadanie. Wyszukaj pozycję **Wdrożenie obszaru roboczego usługi Synapse,** a następnie wybierz **pozycję Dodaj**.

     ![Dodawanie rozszerzenia](media/add-extension-task.png)

1.  W zadaniu wybierz pozycję **...** obok pola **Szablon,** aby wybrać plik szablonu.

1. Wybierz **pozycję ...** obok pola **Parametry szablonu,** aby wybrać plik parametrów.

1. Wybierz połączenie, grupę zasobów i nazwę docelowego obszaru roboczego. 

1. Wybierz **pozycję ...** obok pola **Zastąp parametry szablonu** i wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

    ![Wdrażanie obszaru roboczego usługi Synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> W scenariuszach z ciasną integracją/cd typ środowiska Integration Runtime (IR) w różnych środowiskach musi być taki sam. Jeśli na przykład masz własne środowisko IR w środowisku dewelopera, to samo środowisko IR musi być również typu self-hosted w innych środowiskach, takich jak środowisko testowe i produkcyjne. Podobnie, jeśli współużytkujemy środowiska Integration Runtime na wielu etapach, należy skonfigurować środowiska Integration Runtime jako połączone samodzielnie hostowane we wszystkich środowiskach, takich jak tworzenie, testowanie i produkcja.

## <a name="create-release-for-deployment"></a>Tworzenie wydania do wdrożenia 

Po zapisaniu wszystkich zmian możesz wybrać pozycję **Utwórz wydanie,** aby ręcznie utworzyć wydanie. Aby zautomatyzować tworzenie wydań, zobacz [wyzwalacze Azure DevOps wydania](/azure/devops/pipelines/release/triggers)

   ![Wybierz pozycję Utwórz wydanie](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Używanie parametrów niestandardowych szablonu obszaru roboczego 

Korzystasz z automatycznej instalacji/wdrażania i chcesz zmienić niektóre właściwości podczas wdrażania, ale właściwości nie są domyślnie sparametryzowane. W takim przypadku można zastąpić domyślny szablon parametrów.

Aby zastąpić domyślny szablon parametrów, należy utworzyć szablon parametrów niestandardowych o nazwie **template-parameters-definition.jsw** folderze głównym gałęzi współpracy usługi Git. Należy użyć tej dokładnej nazwy pliku. Podczas publikowania z gałęzi współpracy obszar roboczy usługi Synapse odczyta ten plik i użyje jego konfiguracji do wygenerowania parametrów. Jeśli plik nie zostanie znaleziony, używany jest domyślny szablon parametrów.

### <a name="custom-parameter-syntax"></a>Składnia parametrów niestandardowych

Poniżej przedstawiono kilka wskazówek dotyczących tworzenia pliku parametrów niestandardowych:

* Wprowadź ścieżkę właściwości w obszarze odpowiedniego typu jednostki.
* Ustawienie nazwy właściwości na wartość wskazuje, że chcesz sparametryzować wszystkie jej właściwości (tylko do pierwszego poziomu, a `*` nie rekursywnie). Można również podać wyjątki od tej konfiguracji.
* Ustawienie wartości właściwości jako ciągu wskazuje, że chcesz sparametryzować właściwość. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może być jednym z tych znaków:
      * `=` Oznacza zachować bieżącą wartość jako wartość domyślną dla parametru.
      * `-` oznacza, że nie zachowaj wartości domyślnej parametru.
      * `|` to specjalny przypadek wpisów tajnych z Azure Key Vault dla kluczy lub parametry połączenia.
   * `<name>` to nazwa parametru. Jeśli pole jest puste, przyjmuje nazwę właściwości . Jeśli wartość rozpoczyna się od `-` znaku, nazwa jest skracana. Na przykład `AzureStorage1_properties_typeProperties_connectionString` termin zostanie skrócony do `AzureStorage1_connectionString` .
   * `<stype>` jest typem parametru. Jeśli `<stype>` wartość jest pusta, domyślnym typem jest `string` . Obsługiwane wartości: `string` , , , , i `securestring` `int` `bool` `object` `secureobject` `array` .
* Określenie tablicy w pliku wskazuje, że właściwość dopasowania w szablonie jest tablicą. Synapse iteruje po wszystkich obiektach w tablicy przy użyciu określonej definicji. Drugi obiekt, ciąg, staje się nazwą właściwości, która jest używana jako nazwa parametru dla każdej iteracji.
* Definicja nie może być specyficzna dla wystąpienia zasobu. Każda definicja ma zastosowanie do wszystkich zasobów tego typu.
* Domyślnie wszystkie bezpieczne ciągi, takie jak Key Vault tajne i bezpieczne ciągi, takie jak parametry połączenia, klucze i tokeny, są sparametryzowane.

### <a name="parameter-template-definition-samples"></a>Przykłady definicji szablonu parametrów 

Oto przykład definicji szablonu parametru:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Poniżej znajduje się wyjaśnienie sposobu konstruowania poprzedniego szablonu z podziałem na typy zasobów.

#### <a name="notebooks"></a>Notebooks 

* Każda właściwość w ścieżce `properties/bigDataPool/referenceName` jest sparametryzowane z jej wartością domyślną. Dla każdego pliku notesu można sparametryzować dołączona pulę platformy Spark. 

#### <a name="sql-scripts"></a>Skrypty SQL 

* Właściwości (poolName i databaseName) w ścieżce są sparametryzowane jako `properties/content/currentConnection` ciągi bez wartości domyślnych w szablonie. 

#### <a name="pipelines"></a>Pipelines

* Każda właściwość w ścieżce `activities/typeProperties/waitTimeInSeconds` jest sparametryzowane. Każde działanie w potoku, które ma właściwość na poziomie kodu o nazwie (na przykład działanie), jest sparametryzowane jako liczba `waitTimeInSeconds` `Wait` z nazwą domyślną. Jednak nie będzie ona mieć wartości domyślnej w szablonie Resource Manager szablonu. Będą to obowiązkowe dane wejściowe podczas Resource Manager wdrożenia.
* Podobnie właściwość o nazwie `headers` (na przykład w działaniu) jest `Web` sparametryzowane z typem `object` (Object). Ma wartość domyślną, która jest taka sama jak wartość fabryki źródłowej.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Wszystkie właściwości w ścieżce `typeProperties` są sparametryzowane przy użyciu odpowiednich wartości domyślnych. Na przykład w obszarze właściwości typu istnieją dwie `IntegrationRuntimes` właściwości: `computeProperties` i `ssisProperties` . Oba typy właściwości są tworzone przy użyciu odpowiednich wartości domyślnych i typów (Object).

#### <a name="triggers"></a>Wyzwalacze

* W `typeProperties` obszarze dwie właściwości są sparametryzowane. Pierwszy z nich to , który jest określony jako wartość domyślna i `maxConcurrency` jest typu `string` . Ma domyślną nazwę parametru `<entityName>_properties_typeProperties_maxConcurrency` .
* Właściwość `recurrence` jest również sparametryzowana. W jego obszarze wszystkie właściwości na tym poziomie są określane jako parametryzowane jako ciągi, z wartościami domyślnymi i nazwami parametrów. Wyjątkiem jest właściwość `interval` , która jest sparametryzowana jako typ `int` . Nazwa parametru ma sufiks `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Podobnie właściwość `freq` jest ciągiem i jest sparametryzowane jako ciąg. Jednak właściwość `freq` jest sparametryzowana bez wartości domyślnej. Nazwa jest skracana i sufiksowana. Na przykład `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Połączone usługi są unikatowe. Połączone usługi i zestawy danych mają szeroką gamę typów, dlatego można zapewnić dostosowanie specyficzne dla typu. W tym przykładzie dla wszystkich połączonych usług typu zostanie zastosowany `AzureDataLakeStore` określony szablon. Dla wszystkich innych (za pośrednictwem `*` ) zostanie zastosowany inny szablon.
* Właściwość `connectionString` zostanie sparametryzowane jako `securestring` wartość. Nie będzie ona mieć wartości domyślnej. Będzie ona mieć skróconą nazwę parametru z sufiksem `connectionString` .
* Właściwość `secretAccessKey` jest właściwością `AzureKeyVaultSecret` (na przykład w połączonej usłudze Amazon S3). Jest on automatycznie sparametryzowane jako Azure Key Vault tajny i pobierany ze skonfigurowanego magazynu kluczy. Można również sparametryzować sam magazyn kluczy.

#### <a name="datasets"></a>Zestawy danych

* Chociaż dostosowywanie specyficzne dla typu jest dostępne dla zestawów danych, konfigurację można zapewnić bez jawnego \* konfigurowania na poziomie . W poprzednim przykładzie wszystkie właściwości zestawu danych w obszarze `typeProperties` są sparametryzowane.


## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ci/cd

Jeśli korzystasz z integracji z usługą Git z obszarem roboczym usługi Synapse i masz potok integracji/cd, który przenosi zmiany z testowania do środowiska testowego, a następnie do środowiska produkcyjnego, zalecamy stosowanie tych najlepszych rozwiązań:

-   **Integracja z usługą Git.** Skonfiguruj tylko obszar roboczy developmentowej usługi Synapse z integracją z usługą Git. Zmiany w testowych i produkcyjnych obszarach roboczych są wdrażane za pośrednictwem integracji/integracji z usługą Git.
-   **Przygotuj pule przed migracją artefaktów.** Jeśli masz skrypt SQL lub notes dołączony do pul w obszarze roboczym projektowania, oczekiwane są takie same nazwy pul w różnych środowiskach. 
-   **Infrastruktura jako kod (IaC).** Zarządzanie infrastrukturą (sieciami, maszynami wirtualnymi, usługami równoważenia obciążenia i topologią połączeń) w modelu opisowym używa takiej samej obsługi wersji, jak w przypadku kodu źródłowego używanego przez zespół DevOps. 
-   **Inne .** Zobacz [najlepsze rozwiązania dotyczące artefaktów ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Rozwiązywanie problemów z wdrażaniem artefaktów 

### <a name="use-the-synapse-workspace-deployment-task"></a>Korzystanie z zadania wdrażania obszaru roboczego usługi Synapse

W uciece Synapse istnieje wiele artefaktów, które nie są zasobami arm. Różni się to od Azure Data Factory. Zadanie wdrażania szablonu usługi ARM nie będzie działać prawidłowo w celu wdrożenia artefaktów usługi Synapse
 
### <a name="unexpected-token-error-in-release"></a>Nieoczekiwany błąd tokenu w wydaniu

Jeśli plik parametrów zawiera wartości parametrów, które nie są ucieczką, potok wydania nie będzie w stanie analizujeć pliku i wygeneruje błąd "nieoczekiwany token". Zalecamy zastąpienie parametrów lub użycie usługi Azure KeyVault do pobrania wartości parametrów. Można również użyć podwójnych znaków ucieczki jako obejścia.
