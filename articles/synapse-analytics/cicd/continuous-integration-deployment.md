---
title: Ciągła integracja i dostarczanie dla obszaru roboczego Synapse
description: Dowiedz się, jak korzystać z ciągłej integracji i dostarczania, aby wdrażać zmiany w obszarze roboczym z jednego środowiska (opracowywanie, testowanie i produkcja).
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561961"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Ciągła integracja i dostarczanie dla obszaru roboczego usługi Azure Synapse

## <a name="overview"></a>Omówienie

Ciągła integracja (CI) to proces automatyzacji kompilowania i testowania kodu za każdym razem, gdy członek zespołu zatwierdza zmiany w kontroli wersji. Ciągłe wdrażanie (CD) to proces kompilowania, testowania, konfigurowania i wdrażania wielu środowisk testowych lub przejściowych w środowisku produkcyjnym.

W przypadku usługi Azure Synapse Workspace, ciągłej integracji i dostarczania (CI/CD) przenosi wszystkie jednostki z jednego środowiska (Programowanie, testowanie, produkcja) do innego. Aby podwyższyć poziom obszaru roboczego do innego obszaru roboczego, istnieją dwie części: Użyj [szablonów Azure Resource Manager](../../azure-resource-manager/templates/overview.md) do tworzenia lub aktualizowania zasobów obszaru roboczego (pul i obszarów roboczych); Migrowanie artefaktów (skrypty SQL, Notes, definicja zadań platformy Spark, potoki, zestawy danych, przepływy, dane itp.) dzięki narzędziom CI/CD Synapse na platformie Azure DevOps. 

W tym artykule zawarto informacje na temat korzystania z potoku wersji platformy Azure w celu zautomatyzowania wdrożenia obszaru roboczego Synapse w wielu środowiskach.

## <a name="prerequisites"></a>Wymagania wstępne

-   Obszar roboczy używany do tworzenia aplikacji został skonfigurowany z repozytorium Git w programie Studio, patrz [Kontrola źródła w programie Synapse Studio](source-control.md).
-   Projekt usługi Azure DevOps został przygotowany do uruchomienia potoku wydania.

## <a name="set-up-a-release-pipelines"></a>Konfigurowanie potoków wydania

1.  W [usłudze Azure DevOps](https://dev.azure.com/)Otwórz projekt utworzony dla tego wydania.

1.  W lewej części strony wybierz pozycję **potoki**, a następnie wybierz pozycję **wersje**.

    ![Wybieranie potoków, wydań](media/create-release-1.png)

1.  Wybierz pozycję **Nowy potok** lub, jeśli masz istniejące potoki, wybierz pozycję **Nowy** , a następnie **nowe potoku wydania**.

1.  Wybierz **pusty szablon zadania** .

    ![Wybierz puste zadanie](media/create-release-select-empty.png)

1.  W polu **Nazwa etapu** wprowadź nazwę środowiska.

1.  Wybierz pozycję **Dodaj artefakt**, a następnie wybierz repozytorium git skonfigurowane przy użyciu programu Development Synapse Studio. Wybierz repozytorium git, które zostało użyte do zarządzania szablonami ARM pul i obszaru roboczego. Jeśli używasz usługi GitHub jako źródła, musisz utworzyć połączenie z usługą dla konta w usłudze GitHub i pobrać repozytoria. Aby uzyskać więcej informacji na temat [połączenia z usługą](/azure/devops/pipelines/library/service-endpoints) 

    ![Dodaj gałąź publikowania](media/release-creation-github.png)

1.  Wybierz gałąź szablonu usługi ARM dla aktualizacji zasobów. W polu **wersja domyślna** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodaj szablon ARM](media/release-creation-arm-branch.png)

1.  Wybierz [gałąź Publikuj](source-control.md#configure-publishing-settings) repozytorium dla **gałęzi domyślnej**. Domyślnie ta gałąź publikowania to `workspace_publish` . W polu **wersja domyślna** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodawanie artefaktu](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Skonfiguruj zadanie etapowe tworzenia i aktualizowania zasobów usługi ARM 

Dodaj zadanie wdrażania Azure Resource Manager, aby tworzyć lub aktualizować zasoby, w tym obszary robocze i pule:

1. W widoku etap wybierz pozycję **Wyświetl zadania etapowe**.

    ![Widok etapu](media/release-creation-stage-view.png)

1. Utwórz nowe zadanie. Wyszukaj **wdrożenie szablonu ARM**, a następnie wybierz pozycję **Dodaj**.

1. W zadaniu wdrażania wybierz subskrypcję, grupę zasobów i lokalizację docelowego obszaru roboczego. W razie potrzeby podaj poświadczenia.

1. Na liście **Akcja** wybierz pozycję **Utwórz lub Zaktualizuj grupę zasobów**.

1. Wybierz przycisk wielokropka (**...**) obok pola **szablon** . Przeglądaj w poszukiwaniu szablonu Azure Resource Manager docelowego obszaru roboczego

1. Wybierz **...** obok pola **Parametry szablonu** , aby wybrać plik parametrów.

1. Wybierz **...** obok pola **Przesłoń parametry szablonu** wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

1. Wybierz opcję **przyrostowy** dla **trybu wdrożenia**.
    
    ![Wdrażanie obszarów roboczych i pul](media/pools-resource-deploy.png)

1. Obowiązkowe Dodaj **Azure PowerShell** do przypisania roli obszaru roboczego Grant i Update. Jeśli używasz potoku wydania do tworzenia obszaru roboczego Synapse, jednostka usługi potoku zostanie dodana jako domyślny administrator obszaru roboczego. Możesz uruchomić program PowerShell, aby przyznać innym kontom dostęp do obszaru roboczego. 
    
    ![Udziel uprawnienia](media/release-creation-grant-permission.png)

 > [!WARNING]
> W trybie ukończenia wdrożenia zasoby istniejące w grupie zasobów, ale nie są określone w nowym szablonie Menedżer zasobów, zostaną **usunięte**. Aby uzyskać więcej informacji, zobacz [tryby wdrażania Azure Resource Manager](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Konfigurowanie zadania etapowego dla wdrożenia artefaktów 

Użyj rozszerzenia [wdrożenia obszaru roboczego Synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) , aby wdrożyć inne elementy w obszarze roboczym usługi Synapse, takie jak zestaw danych, skrypt SQL, Notes, definicja zadania platformy Spark, przepływu danych, potok, połączona usługa, poświadczenia i IR (Integration Runtime).  

1. Wyszukaj i Pobierz rozszerzenie z **witryny Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Pobierz rozszerzenie](media/get-extension-from-market.png)

1. Wybierz organizację, w której ma zostać zainstalowane rozszerzenie. 

     ![Instalowanie rozszerzenia](media/install-extension.png)

1. Upewnij się, że jednostka usługi potoku platformy Azure DevOps otrzymała uprawnienia do subskrypcji, a także została przypisana jako administrator obszaru roboczego dla docelowego miejsca pracy. 

1. Utwórz nowe zadanie. Wyszukaj **wdrożenie obszaru roboczego Synapse**, a następnie wybierz pozycję **Dodaj**.

     ![Dodaj rozszerzenie](media/add-extension-task.png)

1.  W zadaniu wybierz pozycję **...** obok pola **szablon** wybierz plik szablonu.

1. Wybierz **...** obok pola **Parametry szablonu** , aby wybrać plik parametrów.

1. Wybierz połączenie, grupę zasobów i nazwę docelowego obszaru roboczego. 

1. Wybierz **...** obok pola **Przesłoń parametry szablonu** wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

    ![Wdrażanie obszaru roboczego Synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> W scenariuszach ciągłej integracji/ciągłego wdrażania typ środowiska Integration Runtime (IR) w różnych środowiskach musi być taki sam. Na przykład jeśli masz własne środowisko IR w środowisku deweloperskim, ten sam IR musi być również typu samodzielnego w innych środowiskach, na przykład test i środowisko produkcyjne. Podobnie, jeśli udostępniasz środowiska Integration Runtime na wielu etapach, musisz skonfigurować środowisko Integration Runtime jako połączone samodzielnie hostowane we wszystkich środowiskach, takich jak programowanie, testowanie i środowisko produkcyjne.

## <a name="create-release-for-deployment"></a>Utwórz wydanie do wdrożenia 

Po zapisaniu wszystkich zmian możesz wybrać pozycję **Utwórz wydanie** , aby ręcznie utworzyć wydanie. Aby zautomatyzować tworzenie wydań, zobacz [Azure DevOps Release Triggers](/azure/devops/pipelines/release/triggers)

   ![Wybierz pozycję Utwórz wydanie](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Używanie parametrów niestandardowych szablonu obszaru roboczego 

Używasz zautomatyzowanej ciągłej integracji/ciągłego dostarczania i chcesz zmienić niektóre właściwości podczas wdrażania, ale właściwości domyślnie nie są sparametryzowane. W takim przypadku można zastąpić domyślny szablon parametru.

Aby zastąpić domyślny szablon parametru, należy utworzyć niestandardowy szablon parametru, plik o nazwie **template-parameters-definition.js** w folderze głównym gałęzi współpracy usługi git. Należy użyć tej dokładnej nazwy pliku. Podczas publikowania z gałęzi współpracy obszar roboczy Synapse odczyta ten plik i użyje jego konfiguracji do wygenerowania parametrów. Jeśli plik nie zostanie znaleziony, zostanie użyty domyślny szablon parametru.

### <a name="custom-parameter-syntax"></a>Składnia parametru niestandardowego

Poniżej przedstawiono kilka wytycznych dotyczących tworzenia pliku parametrów niestandardowych:

* Wprowadź ścieżkę właściwości pod odpowiednim typem jednostki.
* Ustawienie nazwy właściwości na `*` wskazuje, że chcesz Sparametryzuj wszystkie jej właściwości (tylko do pierwszego poziomu, a nie cyklicznie). Możesz również podać wyjątki dla tej konfiguracji.
* Ustawienie wartości właściwości jako ciągu wskazuje, że chcesz Sparametryzuj właściwość. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może to być jeden z następujących znaków:
      * `=` oznacza, że bieżąca wartość jest zachowywana jako wartość domyślna parametru.
      * `-` oznacza nie zachowywanie wartości domyślnej dla parametru.
      * `|` jest specjalnym przypadkiem dla wpisów tajnych z Azure Key Vault dla parametrów połączenia lub kluczy.
   * `<name>` jest nazwą parametru. Jeśli jest pusta, przyjmuje nazwę właściwości. Jeśli wartość zaczyna się od `-` znaku, nazwa zostanie skrócona. Na przykład `AzureStorage1_properties_typeProperties_connectionString` zostałby skrócony do `AzureStorage1_connectionString` .
   * `<stype>` jest typem parametru. Jeśli `<stype>` pole jest puste, domyślnym typem jest `string` . Obsługiwane wartości: `string` , `securestring` ,,, `int` `bool` `object` `secureobject` i `array` .
* Określenie tablicy w pliku wskazuje, że zgodna właściwość w szablonie jest tablicą. Synapse wykonuje iterację wszystkich obiektów w tablicy przy użyciu określonej definicji. Drugi obiekt, ciąg, będzie nazwą właściwości, która jest używana jako nazwa parametru dla każdej iteracji.
* Definicja nie może być określona dla wystąpienia zasobu. Każda definicja ma zastosowanie do wszystkich zasobów tego typu.
* Domyślnie wszystkie bezpieczne ciągi, takie jak Key Vault Secret, i bezpieczne ciągi, takie jak ciągi połączeń, klucze i tokeny, są sparametryzowane.

### <a name="parameter-template-definition-samples"></a>Przykłady definicji szablonu parametrów 

Oto przykład, jak wygląda definicja szablonu parametrów:

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
Poniżej przedstawiono wyjaśnienie sposobu konstruowania poprzedniego szablonu, podzielonego na typ zasobu.

#### <a name="notebooks"></a>Notebooks 

* Wszystkie właściwości w ścieżce `properties/bigDataPool/referenceName` są sparametryzowane przy użyciu wartości domyślnej. Dla każdego pliku notesu można Sparametryzuj dołączoną pulę platformy Spark. 

#### <a name="sql-scripts"></a>Skrypty SQL 

* Właściwości (PoolName i databaseName) w ścieżce `properties/content/currentConnection` są sparametryzowane jako ciągi bez wartości domyślnych w szablonie. 

#### <a name="pipelines"></a>Pipelines

* Wszystkie właściwości w ścieżce `activities/typeProperties/waitTimeInSeconds` są sparametryzowane. Wszystkie działania w potoku, który ma właściwość poziomu kodu o nazwie `waitTimeInSeconds` (na przykład `Wait` działanie), są sparametryzowane jako liczba z nazwą domyślną. Ale nie będzie on miał wartości domyślnej w szablonie Menedżer zasobów. Będzie to obowiązkowe wejście podczas wdrażania Menedżer zasobów.
* Podobnie właściwość o nazwie `headers` (na przykład w `Web` działaniu) jest sparametryzowana przy użyciu typu `object` (Object). Ma wartość domyślną, która jest taka sama jak wartość dla fabryki źródłowej.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Wszystkie właściwości pod ścieżką `typeProperties` są sparametryzowane z odpowiednimi wartościami domyślnymi. Na przykład w `IntegrationRuntimes` właściwościach typu istnieją dwie właściwości: `computeProperties` i `ssisProperties` . Oba typy właściwości są tworzone z odpowiednimi wartościami domyślnymi i typami (Object).

#### <a name="triggers"></a>Wyzwalacze

* W obszarze `typeProperties` , dwie właściwości są sparametryzowane. Pierwszy z nich to `maxConcurrency` , który jest określony jako ma wartość domyślną i jest typu `string` . Ma nazwę domyślnego parametru `<entityName>_properties_typeProperties_maxConcurrency` .
* `recurrence`Właściwość jest również sparametryzowana. W obszarze IT wszystkie właściwości na tym poziomie są określane jako ciągi, z wartościami domyślnymi i nazwami parametrów. Wyjątek jest `interval` właściwością, która jest sparametryzowane jako typ `int` . Nazwa parametru jest sufiksem `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Podobnie `freq` Właściwość jest ciągiem i jest określana jako ciąg. Jednak `freq` Właściwość jest sparametryzowane bez wartości domyślnej. Nazwa jest skracana i ma sufiks. Na przykład `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Połączone usługi są unikatowe. Ponieważ połączone usługi i zestawy danych mają szeroką gamę typów, można zapewnić dostosowanie specyficzne dla określonego typu. W tym przykładzie dla wszystkich połączonych usług typu `AzureDataLakeStore` , zostanie zastosowany określony szablon. Dla wszystkich innych (za pośrednictwem `*` ) zostanie zastosowany inny szablon.
* `connectionString`Właściwość zostanie sparametryzowana jako `securestring` wartość. Nie będzie ona mieć wartości domyślnej. Nazwa parametru zostanie skrócona z sufiksem `connectionString` .
* Właściwość `secretAccessKey` ma być `AzureKeyVaultSecret` (na przykład w połączonej usłudze Amazon S3). Jest on automatycznie sparametryzowany jako wpis tajny Azure Key Vault i pobierany ze skonfigurowanego magazynu kluczy. Możesz również Sparametryzuj sam magazyn kluczy.

#### <a name="datasets"></a>Zestawy danych

* Chociaż dostosowanie specyficzne dla typu jest dostępne dla zestawów danych, można zapewnić konfigurację bez jawnego \* konfigurowania. W poprzednim przykładzie wszystkie właściwości zestawu danych w obszarze `typeProperties` są sparametryzowane.


## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ciągłej integracji/ciągłego wdrażania

Jeśli korzystasz z integracji narzędzia Git z obszarem roboczym usługi Synapse i masz potoku CI/CD, który przenosi zmiany z programowania na test, a następnie do środowiska produkcyjnego, zalecamy następujące najlepsze rozwiązania:

-   **Integracja** z usługą git. Skonfiguruj tylko obszar roboczy programu Development Synapse z integracją narzędzia Git. Zmiany dotyczące testów i produkcyjnych obszarów roboczych są wdrażane za pośrednictwem ciągłej integracji/ciągłego wdrażania, a nie potrzebna jest integracja z usługą git.
-   **Przygotuj pule przed migracją artefaktów**. Jeśli masz skrypt SQL lub Notes dołączony do pul w obszarze roboczym programowanie, oczekiwana jest taka sama nazwa pul w różnych środowiskach. 
-   **Infrastruktura jako kod (IaC)**. Zarządzanie infrastrukturą (sieciami, maszynami wirtualnymi, modułami równoważenia obciążenia i topologią połączenia) w modelu opisowym przy użyciu tej samej wersji, której zespół DevOps używa w przypadku kodu źródłowego. 
-   **Inne osoby**. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi artefaktów ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Rozwiązywanie problemów z wdrażaniem artefaktów 

### <a name="use-the-synapse-workspace-deployment-task"></a>Korzystanie z zadania wdrażania obszaru roboczego Synapse

W programie Synapse istnieje wiele artefaktów, które nie są zasobami ARM. Różni się to od Azure Data Factory. Zadanie wdrażania szablonu ARM nie będzie działało prawidłowo w celu wdrożenia artefaktów Synapse
 
### <a name="unexpected-token-error-in-release"></a>Nieoczekiwany błąd tokenu w wydaniu

Gdy plik parametrów zawiera wartości parametrów, które nie są wyprowadzane, potok wersji nie będzie mógł przeanalizować pliku i wygeneruje błąd "Nieoczekiwany token". Sugerujemy przesłonięcie parametrów lub użycie usługi Azure kluczy do pobrania wartości parametrów. Można również użyć podwójnego znaku ucieczki jako obejścia.
