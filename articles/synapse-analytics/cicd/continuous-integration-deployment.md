---
title: Ciągła integracja i ciągłe dostarczanie dla obszaru roboczego usługi Synapse
description: Dowiedz się, jak za pomocą ciągłej integracji i dostarczania wdrażać zmiany w obszarze roboczym z jednego środowiska (testowania, testowania, produkcji) w innym.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739678"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Ciągła integracja i ciągłe dostarczanie dla Azure Synapse roboczego

## <a name="overview"></a>Omówienie

Ciągła integracja to proces automatyzowania kompilowania i testowania kodu za każdym razem, gdy członek zespołu zatwierdza zmiany w kontroli wersji. Ciągłe wdrażanie (CD) to proces tworzenia, testowania, konfigurowania i wdrażania z wielu środowisk testowych lub przejściowych do środowiska produkcyjnego.

W obszarze Azure Synapse Analytics ciągłej integracji i ciągłego dostarczania (CI/CD) wszystkie jednostki są przesuwają wszystkie jednostki z jednego środowiska (testowania, testowania, produkcji) do innego. Aby promować obszar roboczy do innego obszaru roboczego, istnieją dwie części. Najpierw użyj szablonu usługi [Azure Resource Manager usługi Arm,](../../azure-resource-manager/templates/overview.md) aby utworzyć lub zaktualizować zasoby obszaru roboczego (pule i obszar roboczy). Następnie przemigruj artefakty (skrypty SQL, notes, definicję zadania platformy Spark, potoki, zestawy danych, przepływy danych itp.) przy użyciu Azure Synapse Analytics ci/CD w Azure DevOps. 

W tym artykule opisano sposób używania potoku Azure DevOps do automatyzowania wdrażania obszaru roboczego Azure Synapse w wielu środowiskach.

## <a name="prerequisites"></a>Wymagania wstępne

Te wymagania wstępne i konfiguracje muszą być spełnione, aby zautomatyzować wdrażanie obszaru Azure Synapse w wielu środowiskach.

### <a name="azure-devops"></a>Azure DevOps

- Projekt Azure DevOps został przygotowany do uruchomienia potoku wydania.
- [Przyznaj wszystkim użytkownikom, którzy będą zaewidencjonić kod "Podstawowy"](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page)dostęp na poziomie organizacji , aby zobaczyli to repo.
- Przyznaj uprawnienia właściciela Azure Synapse do tego Azure Synapse.
- Upewnij się, że utworzono własnego agenta maszyny wirtualnej Azure DevOps lub użyj Azure DevOps hostowanej.
- Uprawnienia do [tworzenia połączenia Azure Resource Manager usługi dla grupy zasobów.](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml)
- Administrator Azure Active Directory usługi (Azure AD) musi zainstalować rozszerzenie [Azure DevOps Workspace Deployment Agent usługi Synapse](/azure/devops/marketplace/install-extension)w organizacji Azure DevOps usługi .
- Utwórz lub wyznaczyj istniejące konto usługi, aby potok był uruchamiany jako. Możesz użyć osobistego tokenu dostępu zamiast konta usługi, ale potoki nie będą działać po usunięciu konta użytkownika.

### <a name="azure-active-directory"></a>Azure Active Directory

- W usłudze Azure AD utwórz jednostkę usługi do użycia na użytek wdrożenia. Zadanie Wdrożenie obszaru roboczego usługi Synapse nie obsługuje korzystania z tożsamości zarządzanej w wersji 1* i starszych.
- W przypadku tej akcji wymagane są uprawnienia administratora usługi Azure AD.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Te wymagania wstępne można zautomatyzować i wdrożyć przy użyciu tego samego potoku, szablonu usługi ARM lub interfejsu wiersza polecenia platformy Azure, ale proces nie został opisany w tym artykule.

- "Źródłowy" obszar roboczy używany do tworzenia aplikacji musi być skonfigurowany za pomocą repozytorium Git w Synapse Studio. Aby uzyskać więcej informacji, zobacz [Kontrola źródła w Synapse Studio](source-control.md#configuration-method-2-manage-hub).

- Pusty obszar roboczy do wdrożenia. Aby skonfigurować pusty obszar roboczy:

  1. Utwórz nowy obszar Azure Synapse Analytics roboczego.
  1. Przyznaj agentowi maszyny wirtualnej i współautorowi jednostki usługi prawa do grupy zasobów, w której jest hostowany nowy obszar roboczy.
  1. W nowym obszarze roboczym nie konfiguruj połączenia z repozytorium Git.
  1. W Azure Portal znajdź nowy obszar roboczy Azure Synapse Analytics, a następnie przyznaj sobie i innym, kto będzie uruchamiać potok Azure DevOps, Azure Synapse Analytics uprawnienia właściciela obszaru roboczego. 
  1. Dodaj agenta Azure DevOps wirtualnej i jednostkę usługi do roli Współautor dla obszaru roboczego (powinien on być dziedziczony, ale sprawdź, czy jest).
  1. W obszarze Azure Synapse Analytics przejdź do obszaru **Zarządzanie** zarządzaniem dostępem i dostępem i  >    >  **dostępem w programie** Studio. Dodaj agenta Azure DevOps wirtualnej i jednostkę usługi do grupy administratorów obszaru roboczego.
  1. Otwórz konto magazynu używane dla obszaru roboczego. W usłudze IAM dodaj agenta maszyny wirtualnej i jednostkę usługi do roli Wybierz współautora danych obiektu blob magazynu.
  1. Utwórz magazyn kluczy w subskrypcji pomocy technicznej i upewnij się, że zarówno istniejący obszar roboczy, jak i nowy obszar roboczy mają co najmniej uprawnienia GET i LIST do magazynu.
  1. Aby wdrożenie automatyczne działało, upewnij się, że wszystkie parametry połączenia określone w połączonych usługach znajdują się w magazynie kluczy.

### <a name="additional-prerequisites"></a>Dodatkowe wymagania wstępne
 
 - Pule platformy Spark i własne środowiska Integration Runtime nie są tworzone w potoku. Jeśli masz usługę połączona, która używa własnego środowiska Integration Runtime, utwórz ją ręcznie w nowym obszarze roboczym.
 - Jeśli tworzysz notesy i masz je połączone z pulą platformy Spark, utwórz ponownie pulę platformy Spark w obszarze roboczym.
 - Notesy połączone z pulą platformy Spark, która nie istnieje w środowisku, nie zostaną wdrożone.
 - Nazwy puli platformy Spark muszą być takie same w obu obszarach roboczych.
 - Nazwij wszystkie bazy danych, pule SQL i inne zasoby tak samo w obu obszarach roboczych.
 - Jeśli aprowizowane pule SQL zostaną wstrzymane podczas próby wdrożenia, wdrożenie może się nie powieść.

Aby uzyskać więcej informacji, zobacz [Ci CD in Azure Synapse Analytics Part 4 - The Release Pipeline (Ci CD in Azure Synapse Analytics Part 4 - The Release Pipeline ( Część 4 — potok wydania).](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434) 


## <a name="set-up-a-release-pipeline"></a>Konfigurowanie potoku wydania

1.  W [Azure DevOps](https://dev.azure.com/)otwórz projekt utworzony dla wydania.

1.  W lewej części strony wybierz pozycję **Potoki,** a następnie wybierz **pozycję Wydania.**

    ![Wybieranie opcji Potoki, Wydania](media/create-release-1.png)

1.  Wybierz **pozycję Nowy potok** lub, jeśli masz istniejące potoki, wybierz pozycję **Nowy,** a następnie **pozycję Nowy potok wydania.**

1.  Wybierz szablon **Puste** zadanie.

    ![Wybierz pozycję Puste zadanie](media/create-release-select-empty.png)

1.  W polu **Nazwa etapu** wprowadź nazwę środowiska.

1.  Wybierz **pozycję Add artifact**(Dodaj artefakt), a następnie wybierz repozytorium git skonfigurowane przy użyciu Synapse Studio. Wybierz repozytorium git używane do zarządzania szablonem ARM pul i obszarów roboczych. Jeśli używasz usługi GitHub jako źródła, musisz utworzyć połączenie z usługą dla swojego konta usługi GitHub i repozytoriów ściągania. Aby uzyskać więcej informacji na temat [połączenia z usługą](/azure/devops/pipelines/library/service-endpoints) 

    ![Dodawanie gałęzi publikowania](media/release-creation-github.png)

1.  Wybierz gałąź szablonu arm dla aktualizacji zasobów. W przypadku **wersji domyślnej** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodawanie szablonu usługi ARM](media/release-creation-arm-branch.png)

1.  Wybierz [gałąź publikowania](source-control.md#configure-publishing-settings) repozytorium dla gałęzi **Default**. Domyślnie ta gałąź publikowania to `workspace_publish` . W przypadku **wersji domyślnej** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodawanie artefaktu](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Konfigurowanie zadania etapowego dla szablonu usługi ARM w celu utworzenia i zaktualizowania zasobu 

Jeśli masz szablon usługi ARM do wdrażania zasobu, takiego jak obszar roboczy usługi Azure Synapse, pule Spark i SQL lub magazyn kluczy, dodaj zadanie wdrożenia usługi Azure Resource Manager, aby utworzyć lub zaktualizować te zasoby:

1. W widoku etapu wybierz pozycję **Wyświetl zadania etapu.**

    ![Widok etapu](media/release-creation-stage-view.png)

1. Utwórz nowe zadanie. Wyszukaj pozycję **Wdrożenie szablonu usługi ARM,** a następnie wybierz **pozycję Dodaj**.

1. W zadaniu Wdrożenie wybierz subskrypcję, grupę zasobów i lokalizację docelowego obszaru roboczego. W razie potrzeby podaj poświadczenia.

1. Na liście **Akcja** wybierz pozycję **Utwórz lub zaktualizuj grupę zasobów.**

1. Wybierz przycisk wielokropka (**...**) obok **pola** Szablon. Wyszukiwanie szablonu Azure Resource Manager docelowego obszaru roboczego

1. Wybierz **pozycję ...** obok pola **Parametry szablonu,** aby wybrać plik parametrów.

1. Wybierz **pozycję ...** obok pola **Zastąp parametry szablonu** i wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

1. Wybierz **pozycję Przyrostowe** dla **opcji Tryb wdrażania.**
    
    ![wdrażanie obszaru roboczego i pul](media/pools-resource-deploy.png)

1. (Opcjonalnie) Dodaj **Azure PowerShell** przypisania roli obszaru roboczego przyznawania i aktualizowania. Jeśli używasz potoku wydania do utworzenia obszaru roboczego usługi Synapse, jednostkę usługi potoku dodaje się jako domyślny administrator obszaru roboczego. Możesz uruchomić program PowerShell, aby udzielić innym kontom dostępu do obszaru roboczego. 
    
    ![udzielanie uprawnień](media/release-creation-grant-permission.png)

 > [!WARNING]
> W trybie ukończenia wdrażania zasoby, które istnieją w grupie zasobów, ale nie są określone w nowym szablonie Resource Manager zostaną **usunięte.** Aby uzyskać więcej informacji, zobacz [Azure Resource Manager Tryby wdrażania](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Konfigurowanie zadania etapu dla wdrożenia artefaktów usługi Synapse 

Użyj rozszerzenia wdrażania obszaru roboczego [usługi Synapse,](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) aby wdrożyć inne elementy w obszarze roboczym usługi Synapse, takie jak zestaw danych, skrypt SQL, notes, definicja zadania platformy Spark, przepływ danych, potok, połączona usługa, poświadczenia i Integration Runtime.  

1. Wyszukaj i pobierz rozszerzenie z witryny **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Uzyskiwanie rozszerzenia](media/get-extension-from-market.png)

1. Wybierz organizację do zainstalowania rozszerzenia. 

     ![Instalowanie rozszerzenia](media/install-extension.png)

1. Upewnij się Azure DevOps że jednostki usługi potoku zostały przyznane uprawnienia subskrypcji i przypisane jako administrator obszaru roboczego dla docelowego obszaru roboczego. 

1. Utwórz nowe zadanie. Wyszukaj pozycję **Wdrożenie obszaru roboczego usługi Synapse,** a następnie wybierz **pozycję Dodaj**.

     ![Dodawanie rozszerzenia](media/add-extension-task.png)

1.  W zadaniu wybierz pozycję **...** obok pola **Szablon,** aby wybrać plik szablonu.

1. Wybierz **pozycję ...** obok pola **Parametry szablonu,** aby wybrać plik parametrów.

1. Wybierz połączenie, grupę zasobów i nazwę docelowego obszaru roboczego. 

1. Wybierz **pozycję ...** obok pola **Zastąp parametry szablonu** i wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego, w tym parametry połączenia i klucze kont używane w połączonych usługach. [Kliknij tutaj, aby uzyskać więcej informacji] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Wdrażanie obszaru roboczego usługi Synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> W scenariuszach ci/CD typ środowiska Integration Runtime (IR) w różnych środowiskach musi być taki sam. Jeśli na przykład masz własne środowisko IR w środowisku projektowym, to samo środowisko IR musi być również typu samodzielnie hostowane w innych środowiskach, takich jak środowisko testowe i produkcyjne. Podobnie, jeśli współużytkujemy środowiska Integration Runtime na wielu etapach, należy skonfigurować środowiska Integration Runtime jako połączone samodzielnie hostowane we wszystkich środowiskach, takich jak tworzenie, testowanie i produkcja.

## <a name="create-release-for-deployment"></a>Tworzenie wydania do wdrożenia 

Po zapisaniu wszystkich zmian możesz wybrać pozycję **Utwórz wydanie,** aby ręcznie utworzyć wydanie. Aby zautomatyzować tworzenie wydań, zobacz [wyzwalacze Azure DevOps wydania](/azure/devops/pipelines/release/triggers)

   ![Wybierz pozycję Utwórz wydanie](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Używanie parametrów niestandardowych szablonu obszaru roboczego 

Korzystasz z automatycznej konfiguracji ci/cd i chcesz zmienić niektóre właściwości podczas wdrażania, ale właściwości nie są domyślnie sparametryzowane. W takim przypadku można zastąpić domyślny szablon parametrów.

Aby zastąpić domyślny szablon parametrów, należy utworzyć szablon parametrów niestandardowych, plik o nazwie **template-parameters-definition.jsw** folderze głównym gałęzi współpracy usługi Git. Należy użyć tej dokładnej nazwy pliku. Podczas publikowania z gałęzi współpracy obszar roboczy usługi Synapse odczyta ten plik i użyje jego konfiguracji do wygenerowania parametrów. Jeśli plik nie zostanie znaleziony, używany jest domyślny szablon parametrów.

### <a name="custom-parameter-syntax"></a>Składnia parametrów niestandardowych

Poniżej przedstawiono kilka wskazówek dotyczących tworzenia pliku parametrów niestandardowych:

* Wprowadź ścieżkę właściwości w obszarze odpowiedniego typu jednostki.
* Ustawienie nazwy właściwości na wartość wskazuje, że chcesz sparametryzować wszystkie jej właściwości (tylko do pierwszego poziomu, a nie `*` rekursywnie). Można również podać wyjątki od tej konfiguracji.
* Ustawienie wartości właściwości jako ciągu wskazuje, że chcesz sparametryzować właściwość. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może być jednym z tych znaków:
      * `=` Oznacza zachować bieżącą wartość jako wartość domyślną parametru.
      * `-` oznacza, że nie zachowaj wartości domyślnej dla parametru .
      * `|` Jest to specjalny przypadek wpisów tajnych z Azure Key Vault dla kluczy lub parametry połączenia.
   * `<name>` to nazwa parametru. Jeśli pole jest puste, przyjmuje nazwę właściwości . Jeśli wartość rozpoczyna się od `-` znaku, nazwa jest skracana. Na przykład termin `AzureStorage1_properties_typeProperties_connectionString` zostanie skrócony do `AzureStorage1_connectionString` .
   * `<stype>` jest typem parametru . Jeśli `<stype>` wartość jest pusta, domyślnym typem jest `string` . Obsługiwane wartości: `string` , , , , i `securestring` `int` `bool` `object` `secureobject` `array` .
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

* W `typeProperties` obszarze dwie właściwości są sparametryzowane. Pierwszy z nich to , który jest określony jako ma wartość domyślną i `maxConcurrency` jest typu `string` . Ma domyślną nazwę parametru `<entityName>_properties_typeProperties_maxConcurrency` .
* Właściwość `recurrence` jest również sparametryzowana. W jego obszarze wszystkie właściwości na tym poziomie są określane jako parametryzowane jako ciągi, z wartościami domyślnymi i nazwami parametrów. Wyjątkiem jest właściwość `interval` , która jest sparametryzowana jako typ `int` . Nazwa parametru ma sufiks `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Podobnie właściwość `freq` jest ciągiem i jest sparametryzowane jako ciąg. Jednak właściwość `freq` jest sparametryzowana bez wartości domyślnej. Nazwa jest skracana i sufiksowana. Na przykład `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Połączone usługi są unikatowe. Połączone usługi i zestawy danych mają szeroką gamę typów, dlatego można zapewnić dostosowanie specyficzne dla typu. W tym przykładzie dla wszystkich połączonych usług typu zostanie zastosowany `AzureDataLakeStore` określony szablon. Dla wszystkich innych (za pośrednictwem `*` ) zostanie zastosowany inny szablon.
* Właściwość `connectionString` zostanie sparametryzowane jako `securestring` wartość. Nie będzie ona mieć wartości domyślnej. Będzie ona mieć skróconą nazwę parametru z sufiksem `connectionString` .
* Właściwość `secretAccessKey` jest właściwością `AzureKeyVaultSecret` (na przykład w połączonej usłudze Amazon S3). Jest on automatycznie sparametryzowane jako Azure Key Vault tajny i pobierany ze skonfigurowanego magazynu kluczy. Można również sparametryzować sam magazyn kluczy.

#### <a name="datasets"></a>Zestawy danych

* Chociaż dostosowywanie specyficzne dla typu jest dostępne dla zestawów danych, konfigurację można zapewnić bez jawnego \* konfigurowania na poziomie . W poprzednim przykładzie wszystkie właściwości zestawu danych w obszarze `typeProperties` są sparametryzowane.


## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ci/cd

Jeśli korzystasz z integracji z usługą Git z obszarem roboczym usługi Azure Synapse i masz potok cicha/cd, który przenosi zmiany z testowania do środowiska testowego, a następnie do środowiska produkcyjnego, zalecamy stosowanie tych najlepszych rozwiązań:

-   **Integracja z usługą Git.** Skonfiguruj tylko obszar roboczy Azure Synapse z integracją z usługą Git. Zmiany w testowych i produkcyjnych obszarach roboczych są wdrażane za pośrednictwem integracji/integracji z usługą Git.
-   **Przygotuj pule przed migracją artefaktów.** Jeśli masz skrypt SQL lub notes dołączony do pul w obszarze roboczym projektowania, oczekiwane są takie same nazwy pul w różnych środowiskach. 
-   **Infrastruktura jako kod (IaC).** Zarządzanie infrastrukturą (sieciami, maszynami wirtualnymi, usługami równoważenia obciążenia i topologią połączeń) w modelu opisowym używa takiej samej obsługi wersji, jak w przypadku kodu źródłowego używanego przez zespół DevOps. 
-   **Inne .** Zobacz [najlepsze rozwiązania dotyczące artefaktów ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Rozwiązywanie problemów z wdrażaniem artefaktów 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Korzystanie z zadania Azure Synapse Analytics obszaru roboczego

W Azure Synapse Analytics istnieją artefakty, które nie są zasobami arm. Różni się to od Azure Data Factory. Zadanie wdrażania szablonu usługi ARM nie będzie działać prawidłowo w celu wdrożenia Azure Synapse Analytics artefaktów.
 
### <a name="unexpected-token-error-in-release"></a>Nieoczekiwany błąd tokenu w wydaniu

Jeśli plik parametrów zawiera wartości parametrów, które nie są ucieczką, potok wydania nie będzie w stanie analizujeć pliku i wygeneruje błąd "nieoczekiwany token". Zalecamy zastąpienie parametrów lub użycie Azure Key Vault do pobierania wartości parametrów. Można również użyć podwójnych znaków ucieczki jako obejścia.
