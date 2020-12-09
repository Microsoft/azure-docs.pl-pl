---
title: Ciągła integracja i dostarczanie w usłudze Azure Data Factory
description: Dowiedz się, jak korzystać z ciągłej integracji i dostarczania, aby przenosić Data Factory potoków z jednego środowiska (Programowanie, testowanie, produkcja) do innego.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 84e156074d6db837556ba4ed9febdb43bcdf3318
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902327"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Ciągła integracja i dostarczanie w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Omówienie

Ciągła integracja to metoda automatycznego testowania każdej zmiany dokonanej w bazie kodu, jak najszybciej. Ciągłe dostarczanie odbywa się przy testowaniu, który jest wykonywany podczas ciągłej integracji i wypycha zmiany do systemu przejściowego lub produkcyjnego.

W Azure Data Factory, ciągła integracja i dostarczanie (CI/CD) oznacza przemieszczenie Data Factory potoków z jednego środowiska (Programowanie, testowanie, produkcja) do innego. Azure Data Factory korzysta z [szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md) do przechowywania konfiguracji różnych jednostek ADF (potoków, zestawów danych, przepływów i itp.). Istnieją dwie sugerowane metody podwyższania poziomu fabryki danych do innego środowiska:

-    Automatyczne wdrażanie przy użyciu integracji Data Factory z [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
-    Ręcznie Przekaż szablon Menedżer zasobów przy użyciu integracji środowiska UX Data Factory z Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Cykl życia ciągłej integracji/ciągłego wdrażania

Poniżej znajduje się przykładowy przegląd cyklu życia ciągłej integracji/ciągłego wdrażania w usłudze Azure Data Factory, która jest skonfigurowana Azure Repos git. Aby uzyskać więcej informacji na temat konfigurowania repozytorium git, zobacz [Kontrola źródła w Azure Data Factory](source-control.md).

1.  Fabryka danych programistycznych jest tworzona i konfigurowana za pomocą usługi Azure Repos git. Wszyscy deweloperzy powinni mieć uprawnienia do tworzenia Data Factory zasobów, takich jak potoki i zestawy danych.

1.  Deweloper [tworzy gałąź funkcji](source-control.md#creating-feature-branches) , aby wprowadzić zmianę. Debugują one ich potoku wraz z najnowszymi zmianami. Aby uzyskać więcej informacji na temat debugowania przebiegu potoku, zobacz [iteracyjne programowanie i debugowanie za pomocą Azure Data Factory](iterative-development-debugging.md).

1.  Gdy deweloper zostanie zadowolony ze zmian, tworzy żądanie ściągnięcia z gałęzi funkcji do głównej lub gałęzi współpracy w celu uzyskania zmian przeglądanych przez elementy równorzędne.

1.  Gdy żądanie ściągnięcia zostanie zatwierdzone, a zmiany zostaną scalone w gałęzi głównej, zmiany te zostaną opublikowane do fabryki programistycznej.

1.  Gdy zespół jest gotowy do wdrożenia zmian w fabryce test lub przeprowadzających (testowanie akceptacji użytkowników), zespół przechodzi do swojej wersji Azure Pipelines i wdraża żądaną wersję fabryki deweloperskiej do przeprowadzających. To wdrożenie jest wykonywane w ramach zadania Azure Pipelines i używa Menedżer zasobów szablonu parametrów do zastosowania odpowiedniej konfiguracji.

1.  Po sprawdzeniu zmian w fabryce testowej należy wdrożyć w fabryce produkcyjnej przy użyciu następnego zadania w wersji potoków.

> [!NOTE]
> Tylko fabryka programistyczna jest skojarzona z repozytorium git. Fabryki testowe i produkcyjne nie powinny być skojarzone z repozytorium git i powinny być aktualizowane tylko za pośrednictwem potoku usługi Azure DevOps lub za pomocą szablonu zarządzania zasobami.

Poniższy obraz przedstawia różne kroki tego cyklu życia.

![Diagram ciągłej integracji z Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatyzowanie ciągłej integracji za pomocą wydań Azure Pipelines

Poniżej przedstawiono Przewodnik konfigurowania wersji Azure Pipelines, która automatyzuje wdrażanie fabryki danych w wielu środowiskach.

### <a name="requirements"></a>Wymagania

-   Subskrypcja platformy Azure połączona z programem Visual Studio Team Foundation Server lub Azure Repos, która korzysta z [punktu końcowego usługi Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Fabryka danych skonfigurowana z integracją usługi git Azure Repos.

-   [Magazyn kluczy platformy Azure](https://azure.microsoft.com/services/key-vault/) , który zawiera wpisy tajne dla każdego środowiska.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurowanie wersji Azure Pipelines

1.  W [usłudze Azure DevOps](https://dev.azure.com/)Otwórz projekt, który jest skonfigurowany przy użyciu fabryki danych.

1.  W lewej części strony wybierz pozycję **potoki**, a następnie wybierz pozycję **wersje**.

    ![Wybieranie potoków, wydań](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Wybierz pozycję **Nowy potok** lub, jeśli masz istniejące potoki, wybierz pozycję **Nowy** , a następnie **nowe potoku wydania**.

1.  Wybierz **pusty szablon zadania** .

    ![Wybierz puste zadanie](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  W polu **Nazwa etapu** wprowadź nazwę środowiska.

1.  Wybierz pozycję **Dodaj artefakt**, a następnie wybierz repozytorium git skonfigurowane z Twoją fabryką danych programistycznych. Wybierz [gałąź Publikuj](source-control.md#configure-publishing-settings) repozytorium dla **gałęzi domyślnej**. Domyślnie ta gałąź publikowania to `adf_publish` . W polu **wersja domyślna** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodawanie artefaktu](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Dodawanie Azure Resource Manager zadania wdrażania:

    a.  W widoku etap wybierz pozycję **Wyświetl zadania etapowe**.

    ![Widok etapu](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Utwórz nowe zadanie. Wyszukaj **wdrożenie szablonu ARM**, a następnie wybierz pozycję **Dodaj**.

    c.  W zadaniu wdrażania wybierz subskrypcję, grupę zasobów i lokalizację docelowej fabryki danych. W razie potrzeby podaj poświadczenia.

    d.  Na liście **Akcja** wybierz pozycję **Utwórz lub Zaktualizuj grupę zasobów**.

    e.  Wybierz przycisk wielokropka (**...**) obok pola **szablon** . Wyszukaj szablon Azure Resource Manager, który jest generowany w rozgałęzieniu publikowania skonfigurowanego repozytorium git. Wyszukaj plik `ARMTemplateForFactory.json` w <FactoryName> folderze gałęzi adf_publish.

    f.  Wybierz **...** obok pola **Parametry szablonu** , aby wybrać plik parametrów. Wyszukaj plik `ARMTemplateParametersForFactory.json` w <FactoryName> folderze gałęzi adf_publish.

    przykład  Wybierz **...** obok pola **Zastąp parametry szablonu** i wprowadź odpowiednie wartości parametrów dla docelowej fabryki danych. W przypadku poświadczeń, które pochodzą z Azure Key Vault wprowadź nazwę wpisu tajnego między podwójnymi cudzysłowami. Na przykład, jeśli nazwa wpisu tajnego to cred1, wprowadź wartość **"$ (cred1)"** dla tej wartości.

    h. Wybierz opcję **przyrostowy** dla **trybu wdrożenia**.

    > [!WARNING]
    > W trybie ukończenia wdrożenia zasoby istniejące w grupie zasobów, ale nie są określone w nowym szablonie Menedżer zasobów, zostaną **usunięte**. Aby uzyskać więcej informacji, zobacz [tryby wdrażania Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md)

    ![Data Factory wdrożenie produkcyjne](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Zapisz potok wydania.

1. Aby wyzwolić wydanie, wybierz pozycję **Utwórz wydanie**. Aby zautomatyzować tworzenie wydań, zobacz [Azure DevOps Release Triggers](/azure/devops/pipelines/release/triggers?view=azure-devops)

   ![Wybierz pozycję Utwórz wydanie](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> W scenariuszach ciągłej integracji/ciągłego wdrażania typ środowiska Integration Runtime (IR) w różnych środowiskach musi być taki sam. Na przykład jeśli masz własne środowisko IR w środowisku deweloperskim, ten sam IR musi być również typu samodzielnego w innych środowiskach, na przykład test i środowisko produkcyjne. Podobnie, jeśli udostępniasz środowiska Integration Runtime na wielu etapach, musisz skonfigurować środowisko Integration Runtime jako połączone samodzielnie hostowane we wszystkich środowiskach, takich jak programowanie, testowanie i środowisko produkcyjne.

### <a name="get-secrets-from-azure-key-vault"></a>Pobierz wpisy tajne z Azure Key Vault

Jeśli masz wpisy tajne do przekazania szablonu Azure Resource Manager, zalecamy użycie Azure Key Vault z wersją Azure Pipelines.

Istnieją dwa sposoby obsługi wpisów tajnych:

1.  Dodaj wpisy tajne do pliku Parameters. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](../azure-resource-manager/templates/key-vault-parameter.md).

    Utwórz kopię pliku parametrów, który jest przekazywany do gałęzi Publikuj. Ustaw wartości parametrów, które mają zostać pobrane z Key Vault przy użyciu tego formatu:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    W przypadku korzystania z tej metody wpis tajny jest pobierany automatycznie z magazynu kluczy.

    Plik parametrów musi znajdować się również w rozgałęzieniu publikacji.

1. Dodaj [zadanie Azure Key Vault](/azure/devops/pipelines/tasks/deploy/azure-key-vault) przed zadaniem wdrażania Azure Resource Manager opisanym w poprzedniej sekcji:

    1.  Na karcie **zadania** Utwórz nowe zadanie. Wyszukaj **Azure Key Vault** i dodaj go.

    1.  W Key Vault zadania wybierz subskrypcję, w której został utworzony magazyn kluczy. W razie potrzeby podaj poświadczenia, a następnie wybierz magazyn kluczy.

    ![Dodawanie zadania Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Przyznawanie uprawnień agentowi Azure Pipelines

Jeśli nie ustawiono prawidłowych uprawnień, zadanie Azure Key Vault może zakończyć się niepowodzeniem z powodu błędu odmowy dostępu. Pobierz dzienniki wydania i Znajdź plik. ps1, który zawiera polecenie udzielenia uprawnień agentowi Azure Pipelines. Można uruchomić polecenie bezpośrednio. Można też skopiować Identyfikator podmiotu zabezpieczeń z pliku i ręcznie dodać zasady dostępu w Azure Portal. `Get` i `List` są minimalnymi wymaganymi uprawnieniami.

### <a name="updating-active-triggers"></a>Aktualizowanie aktywnych wyzwalaczy

Wdrożenie może zakończyć się niepowodzeniem, jeśli spróbujesz zaktualizować aktywne wyzwalacze. Aby zaktualizować aktywne wyzwalacze, należy je ręcznie zatrzymać, a następnie uruchomić ponownie po wdrożeniu. Można to zrobić za pomocą Azure PowerShell zadania:

1.  Na karcie **zadania** w wersji dodaj zadanie **Azure PowerShell** . Wybierz zadanie wersja 4. *. 

1.  Wybierz subskrypcję, w której znajduje się Twoja fabryka.

1.  Wybierz **ścieżkę pliku skryptu** jako typ skryptu. Wymaga to zapisania skryptu programu PowerShell w repozytorium. Za pomocą następującego skryptu programu PowerShell można zatrzymać wyzwalacze:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Możesz wykonać podobne kroki (z `Start-AzDataFactoryV2Trigger` funkcją), aby ponownie uruchomić Wyzwalacze po wdrożeniu.

Zespół fabryki danych dostarczył [przykładowego skryptu przedprodukcyjnego i powdrożeniowego](#script) znajdującego się w dolnej części tego artykułu. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Ręczne podwyższenie poziomu szablonu Menedżer zasobów dla każdego środowiska

1. Na liście **szablon ARM** wybierz pozycję **Eksportuj szablon ARM** , aby wyeksportować szablon Menedżer zasobów dla fabryki danych w środowisku deweloperskim.

   ![Eksportowanie szablonu Menedżer zasobów](media/continuous-integration-deployment/continuous-integration-image1.png)

1. W obszarze fabryki danych testowych i produkcyjnych wybierz pozycję **Importuj szablon ARM**. Ta akcja spowoduje przejście do Azure Portal, w którym można zaimportować wyeksportowany szablon. Wybierz opcję **Kompiluj własny szablon w edytorze,** aby otworzyć edytor szablonów Menedżer zasobów.

   ![Tworzenie własnego szablonu](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Wybierz pozycję **Załaduj plik**, a następnie wybierz wygenerowany szablon Menedżer zasobów. Jest to **arm_template.jsw** pliku znajdującym się w pliku zip, który został wyeksportowany w kroku 1.

   ![Edytuj szablon](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. W sekcji ustawienia wprowadź wartości konfiguracyjne, takie jak poświadczenia połączonej usługi. Gdy skończysz, wybierz pozycję **Kup** , aby wdrożyć szablon Menedżer zasobów.

   ![Sekcja ustawień](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Używanie parametrów niestandardowych w szablonach usługi Resource Manager

Jeśli fabryka programistyczna ma skojarzone repozytorium git, można zastąpić domyślne parametry szablonu Menedżer zasobów szablonu Menedżer zasobów wygenerowanego przez opublikowanie lub wyeksportowanie szablonu. Możesz chcieć zastąpić domyślny szablon parametryzacja w następujących scenariuszach:

* Używasz zautomatyzowanej ciągłej integracji/ciągłego dostarczania i chcesz zmienić niektóre właściwości podczas wdrażania Menedżer zasobów, ale właściwości nie są domyślnie sparametryzowane.
* Fabryka jest tak duża, że domyślny szablon Menedżer zasobów jest nieprawidłowy, ponieważ ma więcej niż maksymalna dozwolona liczba parametrów (256).

Aby zastąpić domyślny szablon parametryzacja, przejdź do centrum zarządzania i wybierz **szablon parametryzacja** w sekcji Kontrola źródła. Wybierz pozycję **Edytuj szablon** , aby otworzyć Edytor kodu szablonu parametryzacja. 

![Zarządzanie parametrami niestandardowymi](media/author-management-hub/management-hub-custom-parameters.png)

Tworzenie niestandardowego szablonu parametryzacja tworzy plik o nazwie **arm-template-parameters-definition.js** w folderze głównym gałęzi git. Należy użyć tej dokładnej nazwy pliku.

![Plik parametrów niestandardowych](media/continuous-integration-deployment/custom-parameters.png)

W przypadku publikowania z gałęzi współpracy Data Factory odczyta ten plik i użyje jego konfiguracji do wygenerowania właściwości, które zostaną pobrane. Jeśli plik nie zostanie znaleziony, zostanie użyty szablon domyślny.

Podczas eksportowania szablonu Menedżer zasobów, Data Factory odczytuje ten plik z niezależnej gałęzi, nad którą pracujesz, a nie z gałęzią współpracy. Można utworzyć lub edytować plik z gałęzi prywatnej, gdzie można testować zmiany, wybierając pozycję **Eksportuj szablon ARM** w interfejsie użytkownika. Następnie można scalić plik z gałęzią współpracy.

> [!NOTE]
> Niestandardowy szablon parametryzacja nie zmienia limitu parametru szablonu ARM 256. Umożliwia wybór i zmniejszenie liczby właściwości sparametryzowanych.

### <a name="custom-parameter-syntax"></a>Składnia parametru niestandardowego

Poniżej przedstawiono niektóre wskazówki, które należy wykonać podczas tworzenia pliku parametrów niestandardowych **arm-template-parameters-definition.js**. Plik składa się z sekcji dla każdego typu jednostki: wyzwalacz, potok, połączona usługa, zestaw danych, środowisko Integration Runtime i przepływ danych.

* Wprowadź ścieżkę właściwości pod odpowiednim typem jednostki.
* Ustawienie nazwy właściwości na `*` wskazuje, że chcesz Sparametryzuj wszystkie jej właściwości (tylko do pierwszego poziomu, a nie cyklicznie). Możesz również podać wyjątki dla tej konfiguracji.
* Ustawienie wartości właściwości jako ciągu wskazuje, że chcesz Sparametryzuj właściwość. Użyj formatu `<action>:<name>:<stype>`.
   *  `<action>` może to być jeden z następujących znaków:
      * `=` oznacza, że bieżąca wartość jest zachowywana jako wartość domyślna parametru.
      * `-` oznacza nie zachowywanie wartości domyślnej dla parametru.
      * `|` jest specjalnym przypadkiem dla wpisów tajnych z Azure Key Vault dla parametrów połączenia lub kluczy.
   * `<name>` jest nazwą parametru. Jeśli jest pusta, przyjmuje nazwę właściwości. Jeśli wartość zaczyna się od `-` znaku, nazwa zostanie skrócona. Na przykład `AzureStorage1_properties_typeProperties_connectionString` zostałby skrócony do `AzureStorage1_connectionString` .
   * `<stype>` jest typem parametru. Jeśli `<stype>` pole jest puste, domyślnym typem jest `string` . Obsługiwane wartości: `string` , `securestring` ,,, `int` `bool` `object` `secureobject` i `array` .
* Określenie tablicy w pliku definicji wskazuje, że zgodna właściwość w szablonie jest tablicą. Data Factory wykonuje iterację wszystkich obiektów w tablicy przy użyciu definicji określonej w obiekcie Integration Runtime tablicy. Drugi obiekt, ciąg, będzie nazwą właściwości, która jest używana jako nazwa parametru dla każdej iteracji.
* Definicja nie może być określona dla wystąpienia zasobu. Każda definicja ma zastosowanie do wszystkich zasobów tego typu.
* Domyślnie wszystkie bezpieczne ciągi, takie jak Key Vault Secret, i bezpieczne ciągi, takie jak ciągi połączeń, klucze i tokeny, są sparametryzowane.
 
### <a name="sample-parameterization-template"></a>Przykładowy szablon parametryzacja

Oto przykład tego, jak może wyglądać szablon parametryzacja:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
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
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Poniżej przedstawiono wyjaśnienie sposobu konstruowania poprzedniego szablonu, podzielonego na typ zasobu.

#### <a name="pipelines"></a>Pipelines
    
* Wszystkie właściwości w ścieżce `activities/typeProperties/waitTimeInSeconds` są sparametryzowane. Wszystkie działania w potoku, który ma właściwość poziomu kodu o nazwie `waitTimeInSeconds` (na przykład `Wait` działanie), są sparametryzowane jako liczba z nazwą domyślną. Ale nie będzie on miał wartości domyślnej w szablonie Menedżer zasobów. Będzie to obowiązkowe wejście podczas wdrażania Menedżer zasobów.
* Podobnie właściwość o nazwie `headers` (na przykład w `Web` działaniu) ma wartość sparametryzowane z typem `object` (JObject). Ma wartość domyślną, która jest taka sama jak wartość dla fabryki źródłowej.

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

### <a name="default-parameterization-template"></a>Domyślny szablon parametryzacja

Poniżej znajduje się bieżący domyślny szablon parametryzacja. Jeśli musisz dodać tylko kilka parametrów, edytowanie tego szablonu bezpośrednio może być dobrym pomysłem, ponieważ nie utracisz istniejącej struktury parametryzacja.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Przykład: parametryzacja istniejący Azure Databricks interaktywny identyfikator klastra

Poniższy przykład pokazuje, jak dodać pojedynczą wartość do domyślnego szablonu parametryzacja. Chcemy dodać do pliku parametrów istniejący Azure Databricks interaktywny identyfikator klastra dla połączonej usługi datakostki. Należy zauważyć, że ten plik jest taki sam jak poprzedni plik, z wyjątkiem tego, że jest to uzupełnienie `existingClusterId` pola właściwości `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Połączone szablony Menedżer zasobów

Jeśli konfiguracja ciągłej integracji/ciągłego dostarczania dla fabryk danych, możesz przekroczyć limity szablonów Azure Resource Manager, gdy fabryka rośnie. Na przykład jeden limit to maksymalna liczba zasobów w szablonie Menedżer zasobów. Aby uwzględnić duże fabryki podczas generowania pełnego szablonu Menedżer zasobów dla fabryki, Data Factory teraz generuje połączone szablony Menedżer zasobów. W przypadku tej funkcji cały ładunek fabryki jest podzielony na kilka plików, dzięki czemu nie ograniczenia są ograniczone.

W przypadku skonfigurowania usługi git połączone szablony są generowane i zapisywane wraz z pełnymi szablonami Menedżer zasobów w gałęzi adf_publish w nowym folderze o nazwie linkedTemplates:

![Folder połączonych Menedżer zasobów szablonów](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Połączone szablony Menedżer zasobów zwykle składają się z szablonu głównego i zestawu szablonów podrzędnych, które są połączone z serwerem głównym. Szablon nadrzędny ma nazwę ArmTemplate_master.json, a szablony podrzędne są nazwane przy użyciu wzorca ArmTemplate_0.json, ArmTemplate_1.json i tak dalej. 

Aby korzystać z szablonów połączonych zamiast szablonu pełnego Menedżer zasobów, zaktualizuj zadanie ciągłej integracji/ciągłego dostarczania, aby wskazywało ArmTemplate_master.js, zamiast ArmTemplateForFactory.json (pełny szablon Menedżer zasobów). Menedżer zasobów wymaga również przekazania połączonych szablonów do konta magazynu, aby platforma Azure mogła uzyskać do nich dostęp podczas wdrażania. Aby uzyskać więcej informacji, zobacz [wdrażanie połączonych Menedżer zasobów szablonów przy użyciu programu VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts).

Pamiętaj, aby dodać skrypty Data Factory w potoku ciągłej integracji/ciągłego wdrażania przed i po wykonaniu zadania wdrożenia.

Jeśli nie masz skonfigurowanego narzędzia Git, możesz uzyskać dostęp do połączonych szablonów za pośrednictwem **szablonu** usługi ARM na liście **szablonów ARM** .

## <a name="hotfix-production-environment"></a>Środowisko produkcyjne poprawek

W przypadku wdrożenia fabryki w środowisku produkcyjnym i zapoznania się z usterką, która musi zostać naprawiona natychmiast, ale nie można wdrożyć bieżącej gałęzi współpracy, może być konieczne wdrożenie poprawki. To podejście jest znane jako Szybka naprawa inżynierów lub QFE.

1.    W usłudze Azure DevOps przejdź do wersji, która została wdrożona w środowisku produkcyjnym. Znajdź ostatnie wdrożenie, które zostało wdrożone.

2.    W komunikacie Zatwierdź Uzyskaj identyfikator zatwierdzenia gałęzi współpracy.

3.    Utwórz nową gałąź poprawki z tego zatwierdzenia.

4.    Przejdź do Azure Data Factory środowiska użytkownika i Przełącz się do gałęzi poprawek.

5.    Za pomocą środowiska Azure Data Factory, napraw usterkę. Przetestuj zmiany.

6.    Po zweryfikowaniu poprawki wybierz pozycję **Eksportuj szablon ARM** , aby uzyskać szablon Menedżer zasobów poprawki.

7.    Ręcznie sprawdź tę kompilację w rozgałęzieniu adf_publish.

8.    Jeśli potoku wydania został skonfigurowany do automatycznego wyzwalania na podstawie zaewidencjonowania adf_publish, nowe wydanie zostanie uruchomione automatycznie. W przeciwnym razie ręcznie wydawanie kolejki.

9.    Wdróż wydanie poprawki w fabrykach testowych i produkcyjnych. Ta wersja zawiera poprzedni ładunek produkcyjny oraz poprawkę wykonaną w kroku 5.

10.   Dodaj zmiany z poprawki do gałęzi deweloperskiej, aby późniejsze wersje nie obejmowały tego samego błędu.

Obejrzyj film wideo poniżej szczegółowy samouczek wideo dotyczący sposobu naprawy środowiska. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="exposure-control-and-feature-flags"></a>Kontrola ekspozycji i flagi funkcji

Podczas pracy nad zespołem istnieją wystąpienia, w których można scalać zmiany, ale nie powinny być one uruchamiane w środowiskach z podwyższonym poziomem uprawnień, takich jak produkcja i pytania i odpowiedzi. Aby obsłużyć ten scenariusz, zespół ADF zaleca [DevOps koncepcji używania flag funkcji](/azure/devops/migrate/phase-features-with-feature-flags?view=azure-devops). W module ADF można łączyć [parametry globalne](author-global-parameters.md) i [działanie warunku if](control-flow-if-condition-activity.md) w celu ukrycia zestawów logiki na podstawie tych flag środowiska.

Aby dowiedzieć się, jak skonfigurować flagę funkcji, zobacz poniższy samouczek wideo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IxdW]

## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ciągłej integracji/ciągłego wdrażania

Jeśli korzystasz z integracji narzędzia Git z fabryką danych i masz potok ciągły/CD, który przenosi zmiany z programowania na test, a następnie do środowiska produkcyjnego, zalecamy następujące najlepsze rozwiązania:

-   **Integracja** z usługą git. Skonfiguruj tylko fabrykę danych deweloperskich z integracją git. Zmiany w testach i produkcji są wdrażane za pośrednictwem ciągłej integracji/ciągłego wdrażania.

-   **Skrypt przed wdrożeniem i po wdrożeniu**. Przed etapem wdrażania Menedżer zasobów w obszarze ciągłe/CD należy wykonać określone zadania, takie jak zatrzymywanie i ponowne uruchamianie wyzwalaczy i oczyszczanie. Zalecamy używanie skryptów programu PowerShell przed i po wykonaniu zadania wdrażania. Aby uzyskać więcej informacji, zobacz [Aktualizuj aktywne wyzwalacze](#updating-active-triggers). Zespół fabryki danych [dostarczył skrypt](#script) do użycia w dolnej części tej strony.

-   **Środowisko Integration Runtime i udostępnianie**. Środowiska Integration Runtime nie zmieniają się często i są podobne do wszystkich etapów w ciągłej integracji/ciągłego wdrażania. Dlatego Data Factory oczekuje, że masz taką samą nazwę i typ środowiska Integration Runtime dla wszystkich etapów ciągłej integracji/ciągłego wdrażania. Jeśli chcesz udostępnić środowisko Integration Runtime na wszystkich etapach, rozważ użycie usługi Trzyelementowy Factory, aby zawierała udostępnione środowisko Integration Runtime. Tej fabryki udostępnionej można używać we wszystkich środowiskach jako połączonego typu środowiska Integration Runtime.

-   **Zarządzane wdrożenie prywatnego punktu końcowego**. Jeśli prywatny punkt końcowy już istnieje w fabryce i podjęto próbę wdrożenia szablonu ARM zawierającego prywatny punkt końcowy o tej samej nazwie, ale z zmodyfikowanymi właściwościami, wdrożenie zakończy się niepowodzeniem. Innymi słowy, można pomyślnie wdrożyć prywatny punkt końcowy o ile ma on takie same właściwości jak ten, który już istnieje w fabryce. Jeśli jakakolwiek właściwość różni się między środowiskami, można ją zastąpić, parametryzacja tę właściwość i dostarczając odpowiednią wartość podczas wdrażania.

-   **Key Vault**. W przypadku korzystania z połączonych usług, których informacje o połączeniu są przechowywane w Azure Key Vault, zaleca się przechowywanie oddzielnych magazynów kluczy dla różnych środowisk. Możesz również skonfigurować osobne poziomy uprawnień dla każdego magazynu kluczy. Na przykład użytkownik może nie chcieć, aby członkowie zespołu mieli uprawnienia do wpisów tajnych produkcji. W przypadku przestrzegania tego podejścia zalecamy, aby zachować te same nazwy tajnych na wszystkich etapach. Jeśli zachowasz te same nazwy tajnych, nie musisz Sparametryzuj każdego z parametrów połączenia w środowiskach ciągłej integracji/ciągłego wdrażania, ponieważ jedyną przyczyną zmiany jest nazwa magazynu kluczy, który jest osobnym parametrem.

-  **Nazewnictwo zasobów** Ze względu na ograniczenia dotyczące szablonów ARM mogą wystąpić problemy z wdrożeniem, jeśli Twoje zasoby zawierają spacje w nazwie. Zespół Azure Data Factory zaleca używanie znaków "_" lub "-" zamiast spacji w przypadku zasobów. Na przykład "Pipeline_1" byłoby preferowaną nazwą dla "potok 1".

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

- Zgodnie z projektem Data Factory nie zezwala na wybór zatwierdzeń lub selektywne Publikowanie zasobów. Opublikowanie obejmie wszystkie zmiany wprowadzone w fabryce danych.

    - Jednostki usługi Data Factory są od siebie zależne. Na przykład wyzwalacze są zależne od potoków, a potoki zależą od zestawów danych i innych potoków. Selektywne publikowanie podzestawu zasobów może prowadzić do nieoczekiwanych zachowań i błędów.
    - W rzadkich przypadkach, gdy potrzebna jest publikacja selektywna, rozważ użycie poprawki. Aby uzyskać więcej informacji, zobacz [środowisko produkcyjne poprawki](#hotfix-production-environment).

- Zespół Azure Data Factory nie zaleca przypisywania formantów RBAC platformy Azure do poszczególnych jednostek (potoków, zestawów danych itp.) w fabryce danych. Jeśli na przykład deweloper ma dostęp do potoku lub zestawu danych, powinien mieć dostęp do wszystkich potoków lub zestawów danych w fabryce danych. Jeśli uważasz, że musisz zaimplementować wiele ról platformy Azure w ramach fabryki danych, zapoznaj się z tematem Wdrażanie drugiej fabryki danych.

-   Nie można publikować z gałęzi prywatnych.

-   Obecnie nie można hostować projektów w BitBucket.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Przykładowy skrypt przed wdrożeniem i po wdrożeniu

Następujący przykładowy skrypt może służyć do zatrzymania wyzwalaczy przed wdrożeniem i ponownego uruchamiania ich później. Skrypt zawiera również kod służący do usuwania zasobów, które zostały usunięte. Zapisz skrypt w repozytorium git usługi Azure DevOps i odwołuje się do niego za pośrednictwem zadania Azure PowerShell przy użyciu wersji 4. *.

Podczas uruchamiania skryptu przed wdrożeniem należy określić zmianę następujących parametrów w polu **argumenty skryptu** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Podczas uruchamiania skryptu powdrożeniowego należy określić odmianę następujących parametrów w polu **argumenty skryptu** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Zadanie programu Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Oto skrypt, którego można użyć do wykonania wstępnego i po wdrożeniu. Konta IT dla usuniętych zasobów i odwołań do zasobów.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
