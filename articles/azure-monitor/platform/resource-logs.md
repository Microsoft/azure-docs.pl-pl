---
title: Dzienniki zasobów platformy Azure
description: Informacje na temat przesyłania strumieniowego dzienników zasobów platformy Azure do obszaru roboczego Log Analytics w programie Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: a2f46440a4214e298bc6d2f3b9c2b5680437ead7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522706"
---
# <a name="azure-resource-logs"></a>Dzienniki zasobów platformy Azure
Dzienniki zasobów platformy Azure to [dzienniki platformy](platform-logs-overview.md) , które zapewniają wgląd w operacje wykonywane w ramach zasobu platformy Azure. Zawartość dzienników zasobów zależy od usługi platformy Azure i typu zasobu. Dzienniki zasobów nie są domyślnie zbierane. Musisz utworzyć ustawienia diagnostyczne dla każdego zasobu platformy Azure, aby wysłać dzienniki zasobów do obszaru roboczego Log Analytics, który ma być używany z [dziennikami Azure monitor](data-platform-logs.md), usługa Azure Event Hubs do przesyłania dalej poza platformą Azure lub do usługi Azure Storage w celu archiwizacji.

Zobacz [Tworzenie ustawień diagnostycznych, aby wysyłać dzienniki platformy i metryki do różnych miejsc docelowych](diagnostic-settings.md) w celu uzyskania szczegółowych informacji na temat tworzenia ustawień diagnostycznych i [wdrażania Azure monitor na dużą skalę przy użyciu Azure Policy](../deploy-scale.md) , aby uzyskać szczegółowe informacje na temat używania Azure Policy do automatycznego tworzenia ustawień diagnostycznych dla każdego tworzonego zasobu platformy Azure.

## <a name="send-to-log-analytics-workspace"></a>Wysyłanie do obszaru roboczego usługi Log Analytics
 Wyślij dzienniki zasobów do obszaru roboczego Log Analytics, aby włączyć funkcje [dzienników Azure monitor](data-platform-logs.md) , które obejmują następujące elementy:

- Skorelowanie danych dzienników zasobów z innymi danymi monitorowania zbieranymi przez Azure Monitor.
- Konsolidowanie wpisów dzienników z wielu zasobów platformy Azure, subskrypcji i dzierżawców w jednej lokalizacji do analizy razem.
- Za pomocą zapytań dzienników można wykonywać złożone analizy i uzyskiwać szczegółowe informacje o danych dziennika.
- Użyj alertów dziennika z złożoną logiką alertów.

[Utwórz ustawienie diagnostyczne](diagnostic-settings.md) , aby wysłać dzienniki zasobów do obszaru roboczego log Analytics. Te dane są przechowywane w tabelach zgodnie z opisem w temacie [struktura dzienników Azure monitor](./data-platform-logs.md). Tabele używane przez dzienniki zasobów zależą od typu kolekcji używanej przez zasób:

- Diagnostyka Azure — wszystkie dane są zapisywane w tabeli _AzureDiagnostics_ .
- Specyficzne dla zasobów — dane są zapisywane w pojedynczej tabeli dla każdej kategorii zasobu.

### <a name="azure-diagnostics-mode"></a>Tryb diagnostyczny platformy Azure 
W tym trybie wszystkie dane z dowolnego ustawienia diagnostycznego zostaną zebrane w tabeli _AzureDiagnostics_ . Jest to Starsza Metoda używana dzisiaj przez większość usług platformy Azure. Ponieważ wiele typów zasobów wysyła dane do tej samej tabeli, jego schemat jest nadzbiorem schematów wszystkich typów zbieranych danych.

Rozważmy następujący przykład, w którym ustawienia diagnostyczne są zbierane w tym samym obszarze roboczym dla następujących typów danych:

- Dzienniki inspekcji usługi 1 (schemat zawierający kolumny A, B i C)  
- Dzienniki błędów usługi 1 (schemat zawierający kolumny D, E i F)  
- Dzienniki inspekcji usługi 2 (schemat zawierający kolumny G, H i i)  

Tabela AzureDiagnostics będzie wyglądać następująco:  

| ResourceProvider    | Kategoria     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. Service1 | AuditLogs    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Microsoft. Service1 | ErrorLogs    |    |    |    | pierwszym | W1 | E1 |    |    |    |
| Microsoft. Językowej2 | AuditLogs    |    |    |    |    |    |    | J1 | K1 | L1 |
| Microsoft. Service1 | ErrorLogs    |    |    |    | Q2 | W2 | punktu |    |    |    |
| Microsoft. Językowej2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | Scale |
| Microsoft. Service1 | AuditLogs    | x5 | Y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Specyficzne dla zasobów
W tym trybie poszczególne tabele w wybranym obszarze roboczym są tworzone dla każdej kategorii wybranej w ustawieniu diagnostyki. Ta metoda jest zalecana, ponieważ znacznie ułatwia pracę z danymi w dziennikach zapytań, zapewnia lepszą wykrywalność schematów i ich strukturę, zwiększa wydajność zarówno opóźnienia pozyskiwania, jak i czasy wykonywania zapytań, a także umożliwia przyznanie praw usługi Azure RBAC w określonej tabeli. Wszystkie usługi platformy Azure zostaną ostatecznie przemigrowane do trybu Resource-Specific. 

W powyższym przykładzie powstaje trzy tabele:
 
- *Service1AuditLogs* tabeli w następujący sposób:

    | Dostawca zasobów | Kategoria | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | Y1 | z1 |
    | Service1 | AuditLogs | x5 | Y5 | z5 |
    | ... |

- *Service1ErrorLogs* tabeli w następujący sposób:  

    | Dostawca zasobów | Kategoria | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  pierwszym | W1 | E1 |
    | Service1 | ErrorLogs |  Q2 | W2 | punktu |
    | ... |

- *Service2AuditLogs* tabeli w następujący sposób:  

    | Dostawca zasobów | Kategoria | G | H | I |
    | -- | -- | -- | -- | -- |
    | Językowej2 | AuditLogs | J1 | K1 | L1|
    | Językowej2 | AuditLogs | j3 | k3 | Scale|
    | ... |



### <a name="select-the-collection-mode"></a>Wybierz tryb kolekcji
Większość zasobów platformy Azure będzie zapisywać dane w obszarze roboczym w trybie **diagnostycznym platformy Azure** lub na **konkretnym poziomie zasobów** bez konieczności wyboru. Zapoznaj się z [dokumentacją dla każdej usługi](./resource-logs-schema.md) , aby uzyskać szczegółowe informacje na temat używanego trybu. Wszystkie usługi platformy Azure ostatecznie będą korzystać z Resource-Specific trybie. W ramach tego przejścia niektóre zasoby umożliwią wybranie trybu w ustawieniu diagnostyki. Określ tryb specyficzny dla zasobów dla nowych ustawień diagnostycznych, ponieważ ułatwia to zarządzanie danymi i może ułatwić uniknięcie złożonych migracji w późniejszym czasie.
  
   ![Selektor trybu ustawień diagnostycznych](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Aby zapoznać się z przykładem ustawienia trybu kolekcji przy użyciu szablonu usługi Resource Manager, zobacz [przykłady Menedżer zasobów szablonów dla ustawień diagnostycznych w Azure monitor](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


Istniejące ustawienie diagnostyczne można zmodyfikować z trybem specyficznym dla zasobów. W takim przypadku dane, które zostały już zebrane, pozostaną w tabeli _AzureDiagnostics_ , dopóki nie zostanie usunięte zgodnie z ustawieniami przechowywania dla obszaru roboczego. Nowe dane zostaną zebrane w dedykowanej tabeli. Użyj operatora [Union](/azure/kusto/query/unionoperator) do wykonywania zapytań dotyczących danych w obu tabelach.

Przejdź do blogu [aktualizacji platformy Azure](https://azure.microsoft.com/updates/) , aby poznać anonse dotyczące usług platformy Azure, które obsługują tryb Resource-Specific.

### <a name="column-limit-in-azurediagnostics"></a>Limit kolumn w AzureDiagnostics
Dla każdej tabeli w dziennikach Azure Monitor istnieje limit właściwości 500. Po osiągnięciu tego limitu wszystkie wiersze zawierające dane z właściwości spoza pierwszej 500 zostaną porzucone w czasie pozyskiwania. Tabela *AzureDiagnostics* jest szczególnie podatna na ten limit, ponieważ obejmuje ona właściwości wszystkich usług platformy Azure, które są w niej zapisywane.

W przypadku zbierania dzienników zasobów z wielu usług _AzureDiagnostics_ może przekroczyć ten limit, a dane zostaną pominięte. Dopóki wszystkie usługi platformy Azure nie obsługują trybu określonego zasobów, należy skonfigurować zasoby do zapisu w wielu obszarach roboczych, aby zmniejszyć prawdopodobieństwo osiągnięcia limitu liczby kolumn 500.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory ze względu na szczegółowy zestaw dzienników to usługa, która jest znana, aby napisać dużą liczbę kolumn i potencjalnie spowodować przekroczenie limitu przez _AzureDiagnostics_ . W przypadku wszystkich ustawień diagnostycznych skonfigurowanych przed włączeniem trybu specyficznego dla zasobów dla każdego działania należy utworzyć nową kolumnę dla każdego unikatowego parametru użytkownika. Zostanie utworzona więcej kolumn z powodu pełnego charakteru danych wejściowych i wyjściowych działań.
 
Aby jak najszybciej użyć trybu określonego dla zasobu, należy przeprowadzić migrację dzienników. Jeśli nie możesz tego zrobić natychmiast, przejściową alternatywą jest izolowanie dzienników Azure Data Factory w ich własnym obszarze roboczym, aby zminimalizować prawdopodobieństwo, że te dzienniki wpływają na inne typy dzienników gromadzone w obszarach roboczych.


## <a name="send-to-azure-event-hubs"></a>Wyślij do Event Hubs platformy Azure
Wyślij dzienniki zasobów do centrum zdarzeń, aby wysłać je poza platformę Azure, na przykład do innych rozwiązań usługi log Analytics SIEM lub innych. Dzienniki zasobów z centrów zdarzeń są używane w formacie JSON z `records` elementem zawierającym rekordy w każdym ładunku. Schemat jest zależny od typu zasobu, zgodnie z opisem w temacie [Common i schemacie specyficzny dla usługi dla dzienników zasobów platformy Azure](resource-logs-schema.md).

Poniżej przedstawiono przykładowe dane wyjściowe z Event Hubs dla dziennika zasobów:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Wysyłanie do usługi Azure Storage
Wyślij dzienniki zasobów do usługi Azure Storage, aby zachować je do archiwizacji. Po utworzeniu ustawienia diagnostycznego kontener magazynu jest tworzony na koncie magazynu zaraz po wystąpieniu zdarzenia w jednej z włączonych kategorii dzienników. Obiekty blob w kontenerze używają następującej konwencji nazewnictwa:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład obiekt BLOB sieciowej grupy zabezpieczeń może mieć nazwę podobną do następującej:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minuta (m = 00) jest zawsze równa 00, ponieważ zdarzenia dziennika zasobów są podzielone na pojedyncze obiekty blob na godzinę.

W PT1H.jsw pliku każde zdarzenie jest przechowywane w następującym formacie. Spowoduje to użycie wspólnego schematu najwyższego poziomu, ale będzie unikatowy dla każdej usługi platformy Azure zgodnie z opisem w [schemacie dzienniki zasobów](./resource-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Dzienniki platformy są zapisywane w usłudze BLOB Storage przy użyciu [wierszy JSON](http://jsonlines.org/), gdzie każde zdarzenie jest wierszem, a znak nowego wiersza wskazuje nowe zdarzenie. Ten format został zaimplementowany w listopadzie 2018. Przed tą datą dzienniki zostały zapisane w magazynie obiektów BLOB jako tablica rekordów JSON, zgodnie z opisem w artykule [przygotowanie do zmiany formatu do Azure monitor dzienników platformy zarchiwizowanych na koncie magazynu](resource-logs-blob-format.md).


## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej na temat dzienników zasobów](platform-logs-overview.md).
* [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](diagnostic-settings.md).