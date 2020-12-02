---
title: Monitorowanie fabryk danych przy użyciu Azure Monitor
description: Dowiedz się, jak używać Azure Monitor do monitorowania potoków Data Factory/Azure przez włączenie dzienników diagnostycznych z informacjami z Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 35d2073dca21b4a0d48a43bed9933bb7549cf8f3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497898"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Data Factory monitorowania i alertów za pomocą Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Aplikacje w chmurze są złożone i mają wiele ruchomych części. Monitory zapewniają dane, aby zapewnić, że aplikacje będą działać w dobrej kondycji. Monitory pomagają również uniknąć potencjalnych problemów i rozwiązywać problemy z poprzednimi. Dane monitorowania umożliwiają uzyskanie szczegółowych informacji o aplikacjach. Ta wiedza ułatwia zwiększenie wydajności aplikacji i łatwość utrzymania. Pomaga również zautomatyzować akcje, które w przeciwnym razie wymagają ręcznej interwencji.

Azure Monitor udostępnia metryki i dzienniki infrastruktury podstawowej dla większości usług platformy Azure. Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. Azure Data Factory (ADF) może zapisywać dzienniki diagnostyczne w Azure Monitor. Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Aby uzyskać więcej informacji, zobacz temat [Azure Monitor — przegląd](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Utrzymywanie Azure Data Factory metryk i danych uruchomienia potoku

Data Factory przechowuje dane przebiegu potoku tylko przez 45 dni. Użyj Azure Monitor, jeśli chcesz przechowywać dane przez dłuższy czas. Monitor umożliwia kierowanie dzienników diagnostycznych do analizy w wielu różnych celach.

* **Konto magazynu**: Zapisz dzienniki diagnostyczne na koncie magazynu na potrzeby inspekcji lub inspekcji ręcznej. Możesz użyć ustawień diagnostycznych, aby określić czas przechowywania w dniach.
* **Centrum zdarzeń**: przesyłanie strumieniowe dzienników do Event Hubs platformy Azure. Dzienniki stają się danymi wejściowymi do usługi partnerskiej/rozwiązania do analizy niestandardowej, takiego jak Power BI.
* **Log Analytics**: Analizuj dzienniki przy użyciu log Analytics. Integracja Data Factory z Azure Monitor jest przydatna w następujących scenariuszach:
  * Chcesz pisać złożone zapytania na rozbudowanym zestawie metryk, które są publikowane przez Data Factory do monitorowania. Możesz tworzyć niestandardowe alerty dla tych zapytań za pośrednictwem monitora.
  * Chcesz monitorować w ramach fabryk danych. Dane można kierować z wielu fabryk danych do jednego obszaru roboczego monitora.

Można również użyć konta magazynu lub przestrzeni nazw centrum zdarzeń, które nie znajduje się w subskrypcji zasobu, który emituje dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć dostęp do obu subskrypcji przy użyciu odpowiedniej kontroli dostępu opartej na rolach (Azure RBAC).

## <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszaru roboczego

Utwórz lub Dodaj ustawienia diagnostyczne dla fabryki danych.

1. W portalu przejdź do pozycji monitorowanie. Wybierz kolejno pozycje **Ustawienia**  >  **Ustawienia diagnostyczne**.

1. Wybierz fabrykę danych, dla której chcesz ustawić ustawienie diagnostyczne.

1. Jeśli w wybranej fabryce danych nie istnieją żadne ustawienia, zostanie wyświetlony monit o utworzenie ustawienia. Wybierz pozycję **Włącz diagnostykę**.

   ![Utwórz ustawienie diagnostyczne, jeśli nie istnieją żadne ustawienia](media/data-factory-monitor-oms/monitor-oms-image1.png)

   W przypadku istniejących ustawień fabryki danych zostanie wyświetlona lista ustawień, które zostały już skonfigurowane w fabryce danych. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.

   ![Dodaj ustawienie diagnostyczne, jeśli istnieją ustawienia](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Podaj nazwę ustawienia, wybierz pozycję **Wyślij do log Analytics**, a następnie wybierz obszar roboczy z **log Analytics obszarze roboczym**.

    * W trybie _Diagnostyka platformy Azure_ przepływ dzienników diagnostycznych przebiega w tabeli _AzureDiagnostics_ .

    * W trybie _specyficznym dla zasobów_ dzienniki diagnostyczne z przepływu Azure Data Factory są przesyłane do następujących tabel:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Możesz wybrać różne dzienniki dotyczące obciążeń, które mają być wysyłane do tabel Log Analytics. Na przykład jeśli w ogóle nie korzystasz z SQL Server Integration Services (SSIS), nie musisz wybierać żadnych dzienników usług SSIS. Jeśli chcesz rejestrować operacje uruchamiania/zatrzymywania i konserwacji w programie SSIS Integration Runtime (IR), możesz wybrać pozycję Dzienniki podczerwieni usług SSIS. W przypadku wywołania wykonywania pakietów SSIS za pośrednictwem języka T-SQL w programie SQL Server Management Studio (SSMS), SQL Server Agent lub innych wystawianych narzędzi można wybrać pozycję Dzienniki pakietów usług SSIS. W przypadku wywołania wykonywania pakietów SSIS za pośrednictwem działań wykonywania pakietów usług SSIS w potokach ADF można wybrać wszystkie dzienniki.

    * W przypadku wybrania opcji _AllMetrics_ są dostępne różne metryki usługi ADF umożliwiające monitorowanie lub zgłaszanie alertów, w tym metryki działań ADF, potoków i uruchomień wyzwalacza, a także dla operacji związanych z usługą SSIS IR i wykonywania pakietów SSIS.

   ![Nadaj nazwę ustawieniom i wybierz obszar roboczy usługi log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Ponieważ tabela dzienników platformy Azure nie może mieć więcej niż 500 kolumn, **zdecydowanie** zalecamy wybranie _trybu określonego dla zasobu_. Aby uzyskać więcej informacji, zobacz [log Analytics znane ograniczenia](../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics).

1. Wybierz pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tej fabryki danych. Dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego, gdy tylko nowe dane zdarzenia zostaną wygenerowane. Gdy zdarzenie jest emitowane i pojawia się w Log Analytics, może upłynąć do 15 minut.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Zainstaluj rozwiązanie Azure Data Factory Analytics z witryny Azure Marketplace

To rozwiązanie zawiera podsumowanie ogólnej kondycji Data Factory, z opcjami przechodzenia do szczegółów i rozwiązywania problemów ze wzorcami nieoczekiwanych zachowań. Dzięki rozbudowanym widokom pól możesz uzyskać wgląd w przetwarzanie kluczy, w tym:

* Błyskawiczne podsumowanie potoku, działania i wyzwalacza fabryki danych
* Możliwość przechodzenia do szczegółów przebiegów działania fabryki danych według typu
* Podsumowanie najlepszych potoków w usłudze Data Factory, błędy działań

1. Przejdź do **portalu Azure Marketplace**, wybierz pozycję Filtr **analizy** i wyszukaj ciąg **Azure Data Factory Analytics (wersja zapoznawcza)**

   ![Przejdź do witryny "Azure Marketplace", wprowadź "filtr analityczny" i wybierz pozycję "Azure Data Factory Analytics (wersja zapoznawcza")](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Szczegółowe informacje na temat **Azure Data Factory Analytics (wersja zapoznawcza)**

   ![Szczegóły dotyczące "Azure Data Factory Analytics (wersja zapoznawcza)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Wybierz pozycję **Utwórz** , a następnie Utwórz lub wybierz **obszar roboczy log Analytics**.

   ![Tworzenie nowego rozwiązania](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Monitoruj metryki Data Factory

Zainstalowanie tego rozwiązania powoduje utworzenie domyślnego zestawu widoków w sekcji skoroszyty w wybranym obszarze roboczym Log Analytics. W związku z tym następujące metryki są włączone:

* Uruchomienia APD-1) uruchomienia potoków przez Data Factory
* Uruchomienia ADF — 2) uruchomienia działań według Data Factory
* Uruchomienia ADF-3) uruchomienia wyzwalacza przez Data Factory
* Błędy ADF — 1) 10 najważniejszych błędów potoku według Data Factory
* Błędy funkcji ADF — 2) pierwsze 10 uruchomienia działań według Data Factory
* Błędy w usłudze ADF — 3) 10 najważniejszych błędów wyzwalaczy według Data Factory
* Statystyka ADF — 1) uruchomienia działań według typu
* Statystyka ADF — 2) uruchomienia wyzwalacza według typu
* Statystyka ADF — 3) maksymalny czas trwania przebiegu potoku

![Okno z wyróżnioną pozycją "skoroszyty (wersja zapoznawcza)" i "AzureDataFactoryAnalytics"](media/data-factory-monitor-oms/monitor-oms-image6.png)

Możesz wizualizować powyższe metryki, przyjrzeć się kwerendom związanym z tymi metrykami, edytować zapytania, tworzyć alerty i podejmować inne akcje.

![Graficzna reprezentacja przebiegów potoku przez fabrykę danych "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (wersja zapoznawcza) wysyła dzienniki diagnostyczne do tabel docelowych _specyficznych dla zasobów_ . Zapytania można pisać dla następujących tabel: _ADFPipelineRun_, _ADFTriggerRun_ i _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Metryki Data Factory

Dzięki monitorowi możesz uzyskać wgląd w wydajność i kondycję obciążeń platformy Azure. Najważniejszym typem danych monitorowania jest Metryka, która jest również nazywana licznikiem wydajności. Metryki są emitowane przez większość zasobów platformy Azure. Monitor oferuje kilka sposobów konfigurowania i używania tych metryk do monitorowania i rozwiązywania problemów.

Poniżej przedstawiono niektóre metryki emitowane przez Azure Data Factory w wersji 2:

| **Metryka**                           | **Nazwa wyświetlana metryki**                  | **Jednostka** | **Typ agregacji** | **Opis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Metryki uruchomień działań           | Liczba    | Łącznie                | Całkowita liczba uruchomień działań, które zostały anulowane w oknie minuty. |
| ActivityFailedRuns                   | Metryki uruchamiania działań zakończonych niepowodzeniem             | Liczba    | Łącznie                | Całkowita liczba uruchomień działania, które zakończyły się niepowodzeniem w oknie minuty. |
| ActivitySucceededRuns                | Metryki uruchamiania działań zakończonych powodzeniem          | Liczba    | Łącznie                | Całkowita liczba uruchomień działania zakończonych powodzeniem w oknie minuty. |
| PipelineCancelledRuns                 | Metryki uruchomień potoku           | Liczba    | Łącznie                | Całkowita liczba uruchomień potoków, które zostały anulowane w oknie minuty. |
| PipelineFailedRuns                   | Metryki uruchomionych potoków zakończonych niepowodzeniem             | Liczba    | Łącznie                | Całkowita liczba uruchomień potoków zakończonych niepowodzeniem w oknie minuty. |
| PipelineSucceededRuns                | Metryki uruchamiania potoków zakończonych powodzeniem          | Liczba    | Łącznie                | Całkowita liczba uruchomień potoków zakończonych powodzeniem w oknie minuty. |
| TriggerCancelledRuns                  | Anulowane metryki uruchamiania wyzwalacza            | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza, które zostały anulowane w oknie minuty. |
| TriggerFailedRuns                    | Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem              | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza zakończonych niepowodzeniem w oknie minuty. |
| TriggerSucceededRuns                 | Wyzwalacze uruchomienia wyzwalają metryki           | Liczba    | Łącznie                | Całkowita liczba uruchomień wyzwalacza zakończonych powodzeniem w oknie minuty. |
| SSISIntegrationRuntimeStartCancelled  | Anulowano metryki uruchamiania środowiska Integration Runtime w środowisku SSIS           | Liczba    | Łącznie                | Łączna liczba uruchomień programu SSIS Integration Runtime, które zostały anulowane w oknie minuty. |
| SSISIntegrationRuntimeStartFailed    | Metryki uruchamiania środowiska Integration Runtime w środowisku SSIS             | Liczba    | Łącznie                | Łączna liczba uruchomień programu SSIS Integration Runtime, które nie powiodły się w oknie minuty. |
| SSISIntegrationRuntimeStartSucceeded | Metryki uruchamiania środowiska Integration Runtime w środowisku SSIS zostały pomyślnie zakończone          | Liczba    | Łącznie                | Łączna liczba uruchomień programu SSIS Integration Runtime, które zakończyły się pomyślnie w oknie minuty. |
| SSISIntegrationRuntimeStopStuck      | Zablokowano metryki zatrzymania środowiska uruchomieniowego usług SSIS Integration Runtime               | Liczba    | Łącznie                | Całkowita liczba zatrzymanych usług SSIS Integration Runtime, które zostały zablokowane w oknie minuty. |
| SSISIntegrationRuntimeStopSucceeded  | Metryki zatrzymania środowiska Integration Runtime w środowisku SSIS zostały pomyślnie zakończone           | Liczba    | Łącznie                | Całkowita liczba zatrzymań programu SSIS Integration Runtime, które zakończyły się pomyślnie w oknie minuty. |
| SSISPackageExecutionCancelled         | Anulowane metryki wykonywania pakietu SSIS  | Liczba    | Łącznie                | Łączna Liczba wykonań pakietów SSIS, które zostały anulowane w oknie minuty. |
| SSISPackageExecutionFailed           | Metryki wykonywania pakietu SSIS zakończone niepowodzeniem    | Liczba    | Łącznie                | Łączna Liczba wykonań pakietów SSIS, które zakończyły się niepowodzeniem w oknie minuty. |
| SSISPackageExecutionSucceeded        | Metryki wykonywania pakietu SSIS zakończone powodzeniem | Liczba    | Łącznie                | Łączna Liczba wykonań pakietów SSIS zakończonych powodzeniem w oknie minuty. |

Aby uzyskać dostęp do metryk, wykonaj instrukcje podane w temacie [Azure monitor Data Platform](../azure-monitor/platform/data-platform.md).

> [!NOTE]
> Emitowane są tylko zdarzenia z ukończonej, wyzwolone działania i uruchomienia potoku. W toku, a uruchomienia debugowania **nie** są emitowane. Z drugiej strony są emitowane zdarzenia ze **wszystkich** wykonań pakietów usług SSIS, w tym te, które są wykonywane i w toku, niezależnie od ich metod wywoływania. Można na przykład wywołać wykonywanie pakietów na platformie Azure z włączoną funkcją SQL Server Data Tools (SSDT) przy użyciu języka T-SQL w programie SSMS, SQL Server Agent lub innych wydzielonych narzędziach, a także jako wyzwalane lub debugowania uruchomienia działań pakietu SSIS w potokach ADF.

## <a name="data-factory-alerts"></a>Alerty Data Factory

Zaloguj się do Azure Portal i wybierz pozycję **Monitoruj**  >  **alerty** , aby utworzyć alerty.

![Alerty w menu portalu](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Tworzenie alertów

1. Wybierz pozycję **+ Nowa reguła alertu** , aby utworzyć nowy Alert.

    ![Nowa reguła alertu](media/monitor-using-azure-monitor/alerts_image4.png)

1. Zdefiniuj warunek alertu.

    > [!NOTE]
    > Upewnij się, że wybrano opcję **wszystkie** na liście rozwijanej **Filtruj według typu zasobu** .

    !["Zdefiniuj warunek alertu" > "Wybierz element docelowy", co spowoduje otwarcie okienka "Wybierz zasób" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Zdefiniuj warunek alertu" > "Dodaj kryteria", co spowoduje otwarcie okienka "Konfigurowanie logiki sygnałów"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Okienko "Konfigurowanie typu sygnału"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Zdefiniuj szczegóły alertu.

    ![Szczegóły alertu](media/monitor-using-azure-monitor/alerts_image8.png)

1. Zdefiniuj grupę akcji.

    ![Tworzenie reguły z wyróżnioną pozycją "Nowa grupa akcji"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Utwórz nową grupę akcji](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurowanie poczty e-mail, wiadomości SMS, wypychania i głosu](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definiowanie grupy akcji](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Konfigurowanie dzienników diagnostycznych za pośrednictwem interfejsu API REST Azure Monitor

### <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Użyj ustawień diagnostycznych, aby skonfigurować dzienniki diagnostyczne dla zasobów nieobliczeniowych. Ustawienia kontrolki zasobu mają następujące funkcje:

* Określają miejsce wysyłania dzienników diagnostycznych. Przykłady obejmują konto usługi Azure Storage, centrum zdarzeń platformy Azure lub dzienniki monitora.
* Określają, które kategorie dzienników są wysyłane.
* Określają, jak długo każda kategoria dziennika ma być przechowywana na koncie magazynu.
* Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbą dni od 1 do 2 147 483 647.
* Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie mają żadnego efektu. Ten stan może wystąpić na przykład wtedy, gdy wybrano opcję tylko Event Hubs lub monitorowanie dzienników.
* Zasady przechowywania są stosowane dziennie. Granica między dniami występuje o północy czasu uniwersalnego skoordynowanego (UTC). Po upływie dnia dzienniki są usuwane z dni, które wykraczają poza zasady przechowywania. Na przykład jeśli masz zasady przechowywania o jeden dzień, na początku dzisiaj dzienniki przed wczoraj zostaną usunięte.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Włączanie dzienników diagnostycznych za pośrednictwem interfejsu API REST Azure Monitor

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Tworzenie lub aktualizowanie ustawień diagnostycznych w interfejsie API REST monitora

##### <a name="request"></a>Żądanie

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Nagłówki

* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` na identyfikator zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji, zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ustaw `Content-Type` nagłówek na `application/json` .
* Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="body"></a>Treść

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Właściwość | Typ | Opis |
| --- | --- | --- |
| **storageAccountId** |Ciąg | Identyfikator zasobu konta magazynu, do którego chcesz wysłać dzienniki diagnostyczne. |
| **serviceBusRuleId** |Ciąg | Identyfikator reguły magistrali usług dla przestrzeni nazw usługi Service Bus, w której chcesz utworzyć Event Hubs na potrzeby przesyłania strumieniowego dzienników diagnostycznych. Identyfikator reguły ma format `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Typ złożony | Tablica ziaren i ich zasad przechowywania. Wartość tej właściwości jest pusta. |
|**Pomiar**| Wartości parametrów przebiegu potoku do przekazanie do wywołanego potoku| Obiekt JSON, który mapuje nazwy parametrów na wartości argumentów. |
| **Dziennik**| Typ złożony| Nazwa kategorii dziennika diagnostycznego dla typu zasobu. Aby uzyskać listę kategorii dzienników diagnostycznych dla zasobu, wykonaj operację pobierania ustawień diagnostycznych. |
| **kategorii**| Ciąg| Tablica kategorii dzienników i ich zasad przechowywania. |
| **timeGrain** | Ciąg | Stopień szczegółowości metryk, które są przechwytywane w formacie czasu trwania ISO 8601. Wartość właściwości musi być równa `PT1M` jednej minucie. |
| **dostępny**| Boolean (wartość logiczna) | Określa, czy dla tego zasobu jest włączona kolekcja kategorii metryki lub dziennika. |
| **retentionPolicy**| Typ złożony| Zawiera opis zasad przechowywania dla kategorii Metryka lub dziennik. Ta właściwość jest używana tylko w przypadku kont magazynu. |
|**dni**| int| Liczba dni przechowywania metryk lub dzienników. Jeśli wartość właściwości to 0, dzienniki są przechowywane w nieskończoność. Ta właściwość jest używana tylko w przypadku kont magazynu. |

##### <a name="response"></a>Reakcja

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Uzyskaj informacje na temat ustawień diagnostycznych w interfejsie API REST monitora

##### <a name="request"></a>Żądanie

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Nagłówki

* Zastąp element `{api-version}` pytaniem `2016-09-01`.
* Zamień `{resource-id}` na identyfikator zasobu, dla którego chcesz edytować ustawienia diagnostyczne. Aby uzyskać więcej informacji, zobacz [Używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ustaw `Content-Type` nagłówek na `application/json` .
* Ustaw nagłówek autoryzacji na token sieci Web JSON uzyskany z usługi Azure AD. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie żądań](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="response"></a>Reakcja

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Aby uzyskać więcej informacji, zobacz [Ustawienia diagnostyczne](/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schemat dzienników i zdarzeń

### <a name="monitor-schema"></a>Monitoruj schemat

#### <a name="activity-run-log-attributes"></a>Działanie — atrybuty dziennika uruchamiania

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| **Poziomie** |Ciąg | Poziom dzienników diagnostycznych. W przypadku dzienników uruchomienia działania ustaw wartość właściwości na 4. | `4` |
| **korelacj** |Ciąg | Unikatowy identyfikator śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **pierwszym** | Ciąg | Godzina zdarzenia w formacie czasu UTC `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Ciąg| Identyfikator uruchomienia działania. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Ciąg| Identyfikator uruchomienia potoku. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Identyfikator**| Ciąg | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorii**| Ciąg | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości na `ActivityRuns` . | `ActivityRuns` |
|**poziom**| Ciąg | Poziom dzienników diagnostycznych. Ustaw wartość właściwości na `Informational` . | `Informational` |
|**operationName**| Ciąg | Nazwa działania z jego stanem. Jeśli działanie jest pulsem startowym, wartość właściwości to `MyActivity -` . Jeśli działanie jest pulsem końcowym, wartość właściwości to `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**potokname**| Ciąg | Nazwa potoku. | `MyPipeline` |
|**activityName**| Ciąg | Nazwa działania. | `MyActivity` |
|**Start**| Ciąg | Godzina rozpoczęcia działania jest uruchamiana w formacie czasu UTC. | `2017-06-26T20:55:29.5007959Z`|
|**punktów**| Ciąg | Czas zakończenia działania jest uruchamiany w formacie czasu UTC. Jeśli dziennik diagnostyczny pokazuje, że działanie zostało uruchomione, ale jeszcze nie zostało zakończone, wartość właściwości to `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Potoku — atrybuty dziennika uruchamiania

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| **Poziomie** |Ciąg | Poziom dzienników diagnostycznych. W przypadku dzienników uruchomienia działania ustaw wartość właściwości na 4. | `4` |
| **korelacj** |Ciąg | Unikatowy identyfikator śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **pierwszym** | Ciąg | Godzina zdarzenia w formacie czasu UTC `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Ciąg| Identyfikator uruchomienia potoku. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Identyfikator**| Ciąg | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorii**| Ciąg | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości na `PipelineRuns` . | `PipelineRuns` |
|**poziom**| Ciąg | Poziom dzienników diagnostycznych. Ustaw wartość właściwości na `Informational` . | `Informational` |
|**operationName**| Ciąg | Nazwa potoku wraz z jego stanem. Po zakończeniu uruchomienia potoku wartość właściwości jest równa `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**potokname**| Ciąg | Nazwa potoku. | `MyPipeline` |
|**Start**| Ciąg | Godzina rozpoczęcia działania jest uruchamiana w formacie czasu UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**punktów**| Ciąg | Czas zakończenia działania jest uruchamiany w formacie czasu UTC. Jeśli dziennik diagnostyczny pokazuje działanie, które zostało uruchomione, ale jeszcze nie zostało zakończone, wartość właściwości to `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**Stany**| Ciąg | Końcowy stan uruchomienia potoku. Możliwe wartości właściwości to `Succeeded` i `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Wyzwalacz uruchomienia — atrybuty dziennika

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Właściwość | Typ | Opis | Przykład |
| --- | --- | --- | --- |
| **Poziomie** |Ciąg | Poziom dzienników diagnostycznych. W przypadku dzienników uruchomienia działania ustaw wartość właściwości na 4. | `4` |
| **korelacj** |Ciąg | Unikatowy identyfikator śledzenia określonego żądania. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **pierwszym** | Ciąg | Godzina zdarzenia w formacie czasu UTC `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Ciąg| Identyfikator uruchomienia wyzwalacza. | `08587023010602533858661257311` |
|**Identyfikator**| Ciąg | Identyfikator skojarzony z zasobem fabryki danych. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategorii**| Ciąg | Kategoria dzienników diagnostycznych. Ustaw wartość właściwości na `PipelineRuns` . | `PipelineRuns` |
|**poziom**| Ciąg | Poziom dzienników diagnostycznych. Ustaw wartość właściwości na `Informational` . | `Informational` |
|**operationName**| Ciąg | Nazwa wyzwalacza ze stanem końcowym, który wskazuje, czy wyzwalacz został pomyślnie wywołany. Jeśli puls zakończył się pomyślnie, wartość właściwości to `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| Ciąg | Nazwa wyzwalacza. | `MyTrigger` |
|**triggerType**| Ciąg | Typ wyzwalacza. Możliwe wartości właściwości to `Manual Trigger` i `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| Ciąg | Zdarzenie wyzwalacza. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Start**| Ciąg | Godzina rozpoczęcia wyzwalacza wyzwalana w formacie czasu UTC. | `2017-06-26T20:55:29.5007959Z`|
|**Stany**| Ciąg | Końcowy stan pokazujący, czy wyzwalacz został pomyślnie wywołany. Możliwe wartości właściwości to `Succeeded` i `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>Atrybuty dzienników usług SSIS Integration Runtime

Poniżej przedstawiono atrybuty dziennika operacji uruchamiania/zatrzymywania i konserwacji w środowisku SSIS.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Właściwość                   | Typ   | Opis                                                   | Przykład                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **pierwszym**                   | Ciąg | Godzina zdarzenia w formacie UTC: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Ciąg | Nazwa operacji w podczerwieni usług SSIS                            | `Start/Stop/Maintenance` |
| **kategorii**               | Ciąg | Kategoria dzienników diagnostycznych                               | `SSISIntegrationRuntimeLogs` |
| **korelacj**          | Ciąg | Unikatowy identyfikator śledzenia określonej operacji             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | Ciąg | Nazwa Twojego ADF                                          | `MyADFv2` |
| **integrationRuntimeName** | Ciąg | Nazwa Twojego środowiska IR usług SSIS                                      | `MySSISIR` |
| **poziom**                  | Ciąg | Poziom dzienników diagnostycznych                                  | `Informational` |
| **Result**             | Ciąg | Wynik operacji w podczerwieni SSIS                          | `Started/InProgress/Succeeded/Failed` |
| **Komunikat**                | Ciąg | Komunikat wyjściowy operacji środowiska IR programu SSIS                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **Identyfikator**             | Ciąg | Unikatowy identyfikator zasobu ADF                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Atrybuty dziennika kontekstu komunikatów zdarzeń SSIS

Poniżej znajdują się atrybuty dziennika warunków związanych z komunikatami zdarzeń generowanymi przez wykonywanie pakietów SSIS w środowisku IR usług SSIS. Przekazują one podobne informacje jako [tabelę lub widok kontekstu komunikatów zdarzeń w usłudze SSIS Catalog (SSISDB)](/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15) , który pokazuje wartości czasu wykonywania wielu właściwości pakietu usług SSIS. Są one generowane w przypadku wybrania `Basic/Verbose` poziomu rejestrowania i przydatnego do debugowania/sprawdzania zgodności.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Właściwość                   | Typ   | Opis                                                          | Przykład                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **pierwszym**                   | Ciąg | Godzina zdarzenia w formacie UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Ciąg | Ta wartość jest ustawiona na `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **kategorii**               | Ciąg | Kategoria dzienników diagnostycznych                                      | `SSISPackageEventMessageContext` |
| **korelacj**          | Ciąg | Unikatowy identyfikator śledzenia określonej operacji                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Ciąg | Nazwa Twojego ADF                                                 | `MyADFv2` |
| **integrationRuntimeName** | Ciąg | Nazwa Twojego środowiska IR usług SSIS                                             | `MySSISIR` |
| **poziom**                  | Ciąg | Poziom dzienników diagnostycznych                                         | `Informational` |
| **operationId**            | Ciąg | Unikatowy identyfikator śledzenia określonej operacji w SSISDB          | `1` (1 oznacza operacje związane z pakietami, które **nie** są przechowywane w SSISDB/wywoływane przy użyciu języka T-SQL) |
| **contextDepth**           | Ciąg | Głębokość kontekstu komunikatów zdarzeń                              | `0` (0 oznacza kontekst przed rozpoczęciem wykonywania pakietu, 1 oznacza kontekst, gdy wystąpi błąd, i zwiększa się w miarę jak kontekst jest bardziej z powodu błędu) |
| **packagePath**            | Ciąg | Ścieżka obiektu pakietu jako źródło kontekstu komunikatów zdarzeń      | `\Package` |
| **Element ContextType**            | Ciąg | Typ obiektu pakietu jako źródło kontekstu komunikatów zdarzeń      | `60`(zobacz [więcej typów kontekstu](/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks)) |
| **contextSourceName**      | Ciąg | Nazwa obiektu pakietu jako źródło kontekstu komunikatów zdarzeń      | `MyPackage` |
| **contextSourceId**        | Ciąg | Unikatowy identyfikator obiektu pakietu jako źródło kontekstu komunikatów zdarzeń | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **Funkcja**           | Ciąg | Nazwa właściwości pakietu dla źródła kontekstu komunikatów o zdarzeniach   | `DelayValidation` |
| **Parametr**          | Ciąg | Wartość właściwości Package dla źródła kontekstu komunikatu o zdarzeniu  | `False` |
| **Identyfikator**             | Ciąg | Unikatowy identyfikator zasobu ADF                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Atrybuty dziennika komunikatów zdarzeń usług SSIS

Poniżej znajdują się atrybuty dziennika komunikatów zdarzeń, które są generowane przez wykonania pakietów SSIS w środowisku IR usług SSIS. Przekazują one podobne informacje jako [tabelę lub widok komunikatów zdarzeń SSISDB](/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15) , który pokazuje szczegółowy tekst/metadane komunikatów zdarzeń. Są one generowane na dowolnym poziomie rejestrowania z wyjątkiem `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Właściwość                   | Typ   | Opis                                                        | Przykład                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **pierwszym**                   | Ciąg | Godzina zdarzenia w formacie UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Ciąg | Ta wartość jest ustawiona na `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **kategorii**               | Ciąg | Kategoria dzienników diagnostycznych                                    | `SSISPackageEventMessages` |
| **korelacj**          | Ciąg | Unikatowy identyfikator śledzenia określonej operacji                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Ciąg | Nazwa Twojego ADF                                               | `MyADFv2` |
| **integrationRuntimeName** | Ciąg | Nazwa Twojego środowiska IR usług SSIS                                           | `MySSISIR` |
| **poziom**                  | Ciąg | Poziom dzienników diagnostycznych                                       | `Informational` |
| **operationId**            | Ciąg | Unikatowy identyfikator śledzenia określonej operacji w SSISDB        | `1` (1 oznacza operacje związane z pakietami, które **nie** są przechowywane w SSISDB/wywoływane przy użyciu języka T-SQL) |
| **Czas komunikatu**            | Ciąg | Godzina utworzenia komunikatu o zdarzeniu w formacie UTC          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | Ciąg | Typ komunikatu o zdarzeniu                                     | `70`(zobacz [więcej typów komunikatów](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)) |
| **messageSourceType**      | Ciąg | Typ źródła komunikatu zdarzenia                              | `20`(zobacz [więcej typów źródeł komunikatów](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)) |
| **Komunikat**                | Ciąg | Tekst komunikatu o zdarzeniu                                     | `MyPackage:Validation has started.` |
| **Nr pakietu**            | Ciąg | Nazwa wykonanego pliku pakietu                             | `MyPackage.dtsx` |
| **eventName**              | Ciąg | Nazwa powiązanego zdarzenia czasu wykonywania                                 | `OnPreValidate` |
| **messageSourceName**      | Ciąg | Nazwa składnika pakietu jako źródło komunikatu o zdarzeniu         | `Data Flow Task` |
| **messageSourceId**        | Ciąg | Unikatowy identyfikator składnika pakietu jako źródło komunikatu zdarzenia    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **SubComponentName**       | Ciąg | Nazwa składnika przepływu danych jako źródło komunikatu o zdarzeniu       | `SSIS.Pipeline` |
| **packagePath**            | Ciąg | Ścieżka obiektu pakietu jako źródło komunikatu o zdarzeniu            | `\Package\Data Flow Task` |
| **executionPath**          | Ciąg | Pełna ścieżka z pakietu nadrzędnego do wykonywanego składnika            | `\Transformation\Data Flow Task` (Ta ścieżka również przechwytuje iteracje składników) |
| **threadId**               | Ciąg | Unikatowy identyfikator wątku wykonywany po zarejestrowaniu komunikatu o zdarzeniu | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Atrybuty dziennika statystyk plików SSIS

Poniżej znajdują się atrybuty dziennika statystyk plików wykonywalnych, które są generowane przez wykonania pakietów SSIS w środowisku IR usług SSIS, gdzie pliki wykonywalne są kontenerami lub zadaniami w przepływie sterowania pakietów. Przekazują one podobne informacje jako [tabelę lub widok statystyk SSISDB pliku wykonywalnego](/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15) , który pokazuje wiersz dla każdego uruchomionego pliku wykonywalnego, w tym jego iteracje. Są one generowane na dowolnym poziomie rejestrowania z wyjątkiem `None` i przydatne do identyfikowania wąskich gardeł/awarii na poziomie zadań.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Właściwość                   | Typ   | Opis                                                      | Przykład                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **pierwszym**                   | Ciąg | Godzina zdarzenia w formacie UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Ciąg | Ta wartość jest ustawiona na `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **kategorii**               | Ciąg | Kategoria dzienników diagnostycznych                                  | `SSISPackageExecutableStatistics` |
| **korelacj**          | Ciąg | Unikatowy identyfikator śledzenia określonej operacji                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Ciąg | Nazwa Twojego ADF                                             | `MyADFv2` |
| **integrationRuntimeName** | Ciąg | Nazwa Twojego środowiska IR usług SSIS                                         | `MySSISIR` |
| **poziom**                  | Ciąg | Poziom dzienników diagnostycznych                                     | `Informational` |
| **executionId**            | Ciąg | Unikatowy identyfikator śledzenia określonego wykonania w SSISDB      | `1` (1 oznacza wykonania związane z pakietami, które **nie** są przechowywane w SSISDB/wywoływane przy użyciu języka T-SQL) |
| **executionPath**          | Ciąg | Pełna ścieżka z pakietu nadrzędnego do wykonywanego składnika          | `\Transformation\Data Flow Task` (Ta ścieżka również przechwytuje iteracje składników) |
| **Rozpoczęcia**              | Ciąg | Czas, w którym plik wykonywalny wprowadza fazę sprzed wykonania w formacie UTC  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Ciąg | Czas wprowadzania przez plik wykonywalny fazy po wykonaniu w formacie UTC | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | Ciąg | Czas działania pliku wykonywalnego w milisekundach                   | `1,125` |
| **executionResult**        | Ciąg | Wynik uruchamiania pliku wykonywalnego                                 | `0` (0 oznacza powodzenie, 1 oznacza niepowodzenie, 2 oznacza Kończenie i 3 oznacza anulowanie) |
| **executionValue**         | Ciąg | Zdefiniowana przez użytkownika wartość zwrócona przez uruchomienie pliku wykonywalnego            | `1` |
| **Identyfikator**             | Ciąg | Unikatowy identyfikator zasobu ADF                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Atrybuty dziennika faz składnika wykonywania SSIS

Poniżej znajdują się atrybuty dziennika statystyk czasu wykonywania dla składników przepływu danych, które są generowane przez wykonania pakietów SSIS w środowisku IR usługi SSIS. Przekazują one podobne informacje jako [tabelę lub widok etapów składników wykonywania SSISDB](/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15) , który pokazuje czas spędzony przez składniki przepływu danych we wszystkich etapach wykonywania. Są one generowane w przypadku wybrania `Performance/Verbose` poziomu rejestrowania i przydatnego do przechwytywania statystyk wykonywania przepływu danych.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Właściwość                   | Typ   | Opis                                                         | Przykład                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **pierwszym**                   | Ciąg | Godzina zdarzenia w formacie UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Ciąg | Ta wartość jest ustawiona na `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **kategorii**               | Ciąg | Kategoria dzienników diagnostycznych                                     | `SSISPackageExecutionComponentPhases` |
| **korelacj**          | Ciąg | Unikatowy identyfikator śledzenia określonej operacji                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Ciąg | Nazwa Twojego ADF                                                | `MyADFv2` |
| **integrationRuntimeName** | Ciąg | Nazwa Twojego środowiska IR usług SSIS                                            | `MySSISIR` |
| **poziom**                  | Ciąg | Poziom dzienników diagnostycznych                                        | `Informational` |
| **executionId**            | Ciąg | Unikatowy identyfikator śledzenia określonego wykonania w SSISDB         | `1` (1 oznacza wykonania związane z pakietami, które **nie** są przechowywane w SSISDB/wywoływane przy użyciu języka T-SQL) |
| **Nr pakietu**            | Ciąg | Nazwa wykonanego pliku pakietu                              | `MyPackage.dtsx` |
| **taskName**               | Ciąg | Nazwa wykonanego zadania przepływu danych                                 | `Data Flow Task` |
| **SubComponentName**       | Ciąg | Nazwa składnika przepływu danych                                     | `Derived Column` |
| **wprowadzane**                  | Ciąg | Nazwa fazy wykonywania                                         | `AcquireConnections` |
| **Rozpoczęcia**              | Ciąg | Czas rozpoczęcia fazy wykonywania w formacie UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Ciąg | Czas zakończenia fazy wykonywania w formacie UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | Ciąg | Ścieżka wykonywania zadania przepływu danych                            | `\Transformation\Data Flow Task` |
| **Identyfikator**             | Ciąg | Unikatowy identyfikator zasobu ADF                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Atrybuty dziennika statystyk danych wykonania SSIS

Poniżej znajdują się atrybuty dziennika przesunięć danych przez każdy etap potoków przepływu danych, od nadrzędnych do podrzędnych komponentów, które są generowane przez wykonania pakietów SSIS na urządzeniu SSIS IR. Przekazują one podobne informacje jako [tabelę lub widok statystyk danych wykonawczych SSISDB](/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15) , który pokazuje liczbę wierszy danych przenoszonych przez zadania przepływu danych. Są one generowane w przypadku wybrania `Verbose` poziomu rejestrowania i przydatnego do obliczania przepływności przepływu danych.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Właściwość                     | Typ   | Opis                                                        | Przykład                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **pierwszym**                     | Ciąg | Godzina zdarzenia w formacie UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | Ciąg | Ta wartość jest ustawiona na `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **kategorii**                 | Ciąg | Kategoria dzienników diagnostycznych                                    | `SSISPackageExecutionDataStatistics` |
| **korelacj**            | Ciąg | Unikatowy identyfikator śledzenia określonej operacji                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | Ciąg | Nazwa Twojego ADF                                               | `MyADFv2` |
| **integrationRuntimeName**   | Ciąg | Nazwa Twojego środowiska IR usług SSIS                                           | `MySSISIR` |
| **poziom**                    | Ciąg | Poziom dzienników diagnostycznych                                       | `Informational` |
| **executionId**              | Ciąg | Unikatowy identyfikator śledzenia określonego wykonania w SSISDB        | `1` (1 oznacza wykonania związane z pakietami, które **nie** są przechowywane w SSISDB/wywoływane przy użyciu języka T-SQL) |
| **Nr pakietu**              | Ciąg | Nazwa wykonanego pliku pakietu                             | `MyPackage.dtsx` |
| **taskName**                 | Ciąg | Nazwa wykonanego zadania przepływu danych                                | `Data Flow Task` |
| **dataflowPathIdString**     | Ciąg | Unikatowy identyfikator ścieżki przepływu danych śledzenia                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | Ciąg | Nazwa ścieżki przepływu danych                                         | `ADO NET Source Output` |
| **sourceComponentName**      | Ciąg | Nazwa składnika przepływu danych, który wysyła dane                    | `SQLDB Table3` |
| **destinationComponentName** | Ciąg | Nazwa składnika przepływu danych, który odbiera dane                 | `Derived Column` |
| **rowsSent**                 | Ciąg | Liczba wierszy wysłanych przez składnik źródłowy                        | `500` |
| **createdTime**              | Ciąg | Czas, w którym wartości wierszy są uzyskiwane w formacie UTC                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | Ciąg | Ścieżka wykonywania zadania przepływu danych                           | `\Transformation\Data Flow Task` |
| **Identyfikator**               | Ciąg | Unikatowy identyfikator zasobu ADF                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Schemat Log Analytics

Log Analytics dziedziczy schemat z monitora z następującymi wyjątkami:

* Pierwsze litery w każdej kolumnie są pisane wielkimi literami. Na przykład nazwa kolumny "identyfikator korelacji" w monitorze to "identyfikator korelacji" w Log Analytics.
* Brak kolumny "Level".
* Dynamiczna kolumna "Properties" jest zachowywana jako następujący dynamiczny typ obiektu BLOB JSON.

    | Kolumna Azure Monitor | Kolumna Log Analytics | Typ |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamiczny |
    | $. Properties. Adnotacj | Adnotacje | Dynamiczny |
    | $. Properties. Klawiatur | Dane wejściowe | Dynamiczny |
    | $. Properties. Rozdzielczości | Dane wyjściowe | Dynamiczny |
    | $. Properties. Błąd. errorCode | ErrorCode | int |
    | $. Properties. Błąd. komunikat | ErrorMessage | ciąg |
    | $. Properties. Porn | Błąd | Dynamiczny |
    | $. Properties. Poprzednikami hostowanymi | Poprzednikami hostowanymi | Dynamiczny |
    | $. Properties. Wejściowe | Parametry | Dynamiczny |
    | $.properties.SystemParameters | SystemParameters | Dynamiczny |
    | $. Properties. Tabliczk | Tagi | Dynamiczny |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Monitoruj operacje usług SSIS przy użyciu Azure Monitor

Aby podnieść & przesunięcia obciążeń usług SSIS, możesz [udostępnić środowisko IR usług SSIS w ramach ADF](./tutorial-deploy-ssis-packages-azure.md) , które obsługuje:

- Uruchomione pakiety wdrożone do wykazu usług SSIS (SSISDB) hostowane przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu)
- Uruchomione pakiety wdrożone w systemie plików, Azure Files lub SQL Server Database (MSDB) hostowane przez wystąpienie zarządzane Azure SQL (model wdrażania pakietów)

Po zainicjowaniu obsługi administracyjnej można [sprawdzić stan operacyjny usługi SSIS IR przy użyciu Azure PowerShell lub na koncentratorze **monitora** modułu ADF](./monitor-integration-runtime.md#azure-ssis-integration-runtime). W modelu wdrażania projektu dzienniki wykonywania pakietów usług SSIS są przechowywane w SSISDB wewnętrznych tabelach lub widokach, dzięki czemu możesz wysyłać zapytania, analizować i wizualnie przedstawić je za pomocą wydzielonych narzędzi, takich jak program SSMS. Dzięki modelowi wdrażania pakietów dzienniki wykonywania pakietów SSIS mogą być przechowywane w systemie plików lub Azure Files jako pliki CSV, które nadal trzeba analizować i przetwarzać przy użyciu innych wydzielonych narzędzi, zanim będzie można wykonywać zapytania, analizować i wizualnie je przedstawić.

Teraz dzięki integracji [Azure monitor](../azure-monitor/platform/data-platform.md) możesz wysyłać zapytania, analizować i wizualnie przedstawić wszystkie metryki i dzienniki wygenerowane przez operacje w usłudze SSIS IR i wykonania pakietów ssis na Azure Portal. Ponadto można również zgłaszać na nich alerty.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Konfigurowanie ustawień diagnostycznych i obszaru roboczego dla operacji SSIS

Aby wysłać wszystkie metryki i dzienniki wygenerowane z operacji programu SSIS IR i wykonań pakietów SSIS do Azure Monitor, musisz [skonfigurować ustawienia diagnostyczne i obszar roboczy dla Twojego ADF](#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Metryki operacyjne SSIS

[Metryki](../azure-monitor/platform/data-platform-metrics.md) operacyjne usług SSIS to liczniki wydajności lub wartości liczbowe, które opisują stan operacji uruchamiania i zatrzymywania w środowisku SSIS w środowisku SSI, a także wykonywanie pakietów SSIS w określonym momencie. Są one częścią [metryki ADF w Azure monitor](#data-factory-metrics).

Po skonfigurowaniu ustawień diagnostycznych i obszaru roboczego dla funkcji ADF na Azure Monitor, zaznaczenie pola wyboru _AllMetrics_ spowoduje udostępnienie metryk operacyjnych usług SSIS do [analizy interaktywnej przy użyciu usługi Azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md), [prezentacji na pulpicie nawigacyjnym platformy Azure](../azure-monitor/learn/tutorial-app-dashboards.md)i [alertów niemal](../azure-monitor/platform/alerts-metric.md)w czasie rzeczywistym.

![Nadaj nazwę ustawieniom i wybierz obszar roboczy usługi log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>Alerty operacyjne SSIS

Aby zgłosić alerty dotyczące metryk operacyjnych usług SSIS z portalu usługi ADF, [Wybierz stronę **alerty & metryki** centrum **monitora** ADF i postępuj zgodnie z podanymi instrukcjami krok po kroku](./monitor-visually.md#alerts).

![Wywoływanie alertów operacyjnych SSIS z portalu ADF](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Aby zgłosić alerty dotyczące metryk operacyjnych usług SSIS z Azure Portal, [Wybierz stronę **alerty** usługi Azure **monitor** Hub i postępuj zgodnie z podanymi instrukcjami krok po kroku](#data-factory-alerts).

![Wywoływanie alertów operacyjnych SSIS z Azure Portal](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>Dzienniki operacyjne SSIS

[Dzienniki](../azure-monitor/platform/data-platform-logs.md) operacyjne usług SSIS to zdarzenia generowane przez operacje podczerwieni usług SSIS i wykonania pakietów SSIS, które zapewniają wystarczającą ilość kontekstu dla zidentyfikowanych problemów i są przydatne do analizy głównych przyczyn. 

W przypadku konfigurowania ustawień diagnostycznych i obszaru roboczego dla Twojego ADF na Azure Monitor można wybrać odpowiednie dzienniki operacyjne SSIS i wysłać je do Log Analytics na podstawie Eksplorator danych platformy Azure. W tym miejscu zostaną one udostępnione do [analizy przy użyciu zaawansowanego języka zapytań](../azure-monitor/log-query/log-query-overview.md), [prezentacji na pulpicie nawigacyjnym platformy Azure](../azure-monitor/learn/tutorial-app-dashboards.md)i [alertów niemal](../azure-monitor/platform/alerts-log.md)w czasie rzeczywistym.

![Nadaj nazwę ustawieniom i wybierz obszar roboczy usługi log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

Schematy i zawartość dzienników wykonywania pakietów SSIS w Azure Monitor i Log Analytics są podobne do schematów SSISDB wewnętrznych tabel lub widoków.

| Azure Monitor kategorii dzienników          | Tabele Log Analytics                     | SSISDB wewnętrzne tabele/widoki              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Aby uzyskać więcej informacji na temat atrybutów/właściwości dziennika operacyjnego usług SSIS, zobacz [Azure monitor i log Analytics schematów dla ADF](#schema-of-logs-and-events).

Wybrane dzienniki wykonywania pakietów usług SSIS są zawsze wysyłane do Log Analytics niezależnie od ich metod wywoływania. Można na przykład wywołać wykonywanie pakietów na SSDT z obsługą platformy Azure za pośrednictwem języka T-SQL w programie SSMS, SQL Server Agent lub innych wydzielonych narzędzi, a także jako wyzwalane lub debugowania uruchomienia działań pakietu SSIS w potokach ADF.

Podczas wykonywania zapytania dotyczącego dzienników operacji podczerwieni usług SSIS w usłudze Logs Analytics można użyć właściwości **OperationName** i **ResultType** , które są odpowiednio ustawione jako `Start/Stop/Maintenance` i `Started/InProgress/Succeeded/Failed` . 

![Wykonywanie zapytania dotyczącego dzienników operacji podczerwieni usług SSIS na Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Podczas wykonywania zapytania dotyczącego dzienników wykonywania pakietów SSIS na analizach dzienników można przyłączyć je za pomocą właściwości **OperationId** / **ExecutionID** / **Identyfikator korelacji** . **OperationId** / **ExecutionID** są zawsze ustawione na `1` dla wszystkich operacji/wykonań związanych z pakietami, które **nie** są przechowywane w SSISDB/wywoływane za pośrednictwem T-SQL.

![Wykonywanie zapytania dotyczącego dzienników wykonywania pakietów SSIS na Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Następne kroki
[Programistyczne monitorowanie potoków i zarządzanie nimi](monitor-programmatically.md)