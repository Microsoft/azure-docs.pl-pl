---
title: Archiwizowanie danych z obszaru roboczego Log Analytics w usłudze Azure Storage przy użyciu aplikacji logiki
description: Opisuje metodę, aby użyć Azure Logic Apps do wykonywania zapytań dotyczących danych z obszaru roboczego Log Analytics i wysyłania do usługi Azure Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: ed9942fa7b73418e3ef1ddf0651781d32b662995
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049949"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Archiwizowanie danych z obszaru roboczego Log Analytics w usłudze Azure Storage przy użyciu aplikacji logiki
W tym artykule opisano metodę używania [Azure Logic Apps](../../logic-apps/index.yml) do wykonywania zapytań dotyczących danych z log Analytics obszaru roboczego w Azure monitor i wysyłania do usługi Azure Storage. Tego procesu należy użyć, gdy trzeba wyeksportować dane dziennika Azure Monitor na potrzeby scenariuszy inspekcji i zgodności albo zezwolić innej usłudze na pobieranie tych danych.  

## <a name="other-export-methods"></a>Inne metody eksportowania
Metoda opisana w tym artykule opisuje zaplanowany eksport z zapytania dziennika przy użyciu aplikacji logiki. Inne opcje eksportu danych dla konkretnych scenariuszy obejmują następujące elementy:

- Aby wyeksportować wszystkie dane z obszaru roboczego Log Analytics do konta usługi Azure Storage lub centrum zdarzeń, użyj funkcji eksportu danych obszaru roboczego Log Analytics dzienników Azure Monitor. Zobacz [log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](logs-data-export.md)
- Eksport jednorazowy przy użyciu aplikacji logiki. Zobacz [Azure monitor Logs łącznik Logic Apps i automatyzacji](logicapp-flow-connector.md).
- Jednorazowe Eksportowanie do komputera lokalnego przy użyciu skryptu programu PowerShell. Zobacz [Invoke-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Omówienie
Ta procedura korzysta z [łącznika Azure monitor Logs](https://docs.microsoft.com/connectors/azuremonitorlogs/) , który umożliwia uruchamianie zapytania dziennika z aplikacji logiki i używanie jej w innych akcjach w przepływie pracy. [Łącznik usługi azure BLOB Storage](https://docs.microsoft.com/connectors/azureblob/) jest używany w tej procedurze do wysyłania zapytań wyjściowych do usługi Azure Storage. Inne akcje zostały opisane w poniższych sekcjach.

![Omówienie aplikacji logiki](media/logs-export-logicapp/logic-app-overview.png)

Podczas eksportowania danych z obszaru roboczego Log Analytics należy filtrować i agregowanie danych dziennika oraz zoptymalizować zapytanie, aby ograniczyć ilość danych przetworzonych przez przepływ pracy aplikacji logiki do wymaganych danych. Jeśli na przykład chcesz zarchiwizować zdarzenia logowania, możesz odfiltrować wymagane zdarzenia i projektować tylko wymagane pola z następującym zapytaniem: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Podczas eksportowania danych zgodnie z harmonogramem należy użyć funkcji ingestion_time () w zapytaniu, aby upewnić się, że późne dostarczenie danych nie zostanie pominięte. Jeśli dane są opóźnione ze względu na problemy z siecią lub platformą, korzystanie z czasu pozyskiwania zapewnia, że będzie ono uwzględniane w następnym wykonaniu aplikacji logiki. Zobacz [dodawanie Azure monitor dzienników akcji](#add-azure-monitor-logs-action) na przykład.

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne, które należy wykonać przed wykonaniem tej procedury.

- Log Analytics obszar roboczy. Użytkownik tworzący aplikację logiki musi mieć co najmniej uprawnienia do odczytu w obszarze roboczym. 
- Konto usługi Azure Storage. Konto magazynu nie musi znajdować się w tej samej subskrypcji co obszar roboczy Log Analytics. Użytkownik tworzący aplikację logiki musi mieć uprawnienia do zapisu na koncie magazynu. 


## <a name="connector-limits"></a>Limity łączników
Log Analyticsy obszar roboczy i dziennik zapytań w Azure Monitor są usługami wielodostępnymi, które obejmują limity chroniące i izolowane klientów oraz utrzymujące jakość usług. Podczas wykonywania zapytania dotyczącego dużej ilości danych należy wziąć pod uwagę następujące limity, które mogą mieć wpływ na sposób konfigurowania cyklu aplikacji logiki i zapytania dziennika:

- Zapytania dziennika nie mogą zwracać więcej niż 500 000 wierszy.
- Zapytania dziennika nie mogą zwracać więcej niż 64 000 000 bajtów.
- Zapytania dziennika nie mogą być domyślnie uruchamiane dłużej niż 10 minut. 
- Łącznik Log Analytics jest ograniczony do 100 wywołań na minutę.


## <a name="create-container-in-the-storage-account"></a>Utwórz kontener na koncie magazynu
Użyj procedury opisanej w [sekcji Tworzenie kontenera](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) , aby dodać kontener do konta magazynu w celu przechowywania wyeksportowanych danych. Nazwa użyta dla kontenera w tym artykule to **loganalytics-Data**, ale można użyć dowolnej nazwy.


## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

Przejdź do **Logic Apps** w Azure Portal i kliknij przycisk **Dodaj**. Wybierz **subskrypcję**, **grupę zasobów**i **region** , aby zapisać nową aplikację logiki, a następnie nadaj jej unikatową nazwę. Możesz włączyć ustawienie **log Analytics** , aby zbierać informacje o danych i zdarzeniach środowiska uruchomieniowego zgodnie z opisem w temacie [Konfigurowanie Azure monitor dzienników i zbierać dane diagnostyczne dla Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). To ustawienie nie jest wymagane w przypadku korzystania z łącznika dzienników Azure Monitor.

![Tworzenie aplikacji logiki](media/logs-export-logicapp/create-logic-app.png)


Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz**. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu** , aby otworzyć **projektanta Logic Apps**.

## <a name="create-a-trigger-for-the-logic-app"></a>Tworzenie wyzwalacza dla aplikacji logiki
W obszarze **Rozpocznij od typowego wyzwalacza**wybierz pozycję **cykl**. Spowoduje to utworzenie aplikacji logiki, która jest automatycznie uruchamiana w regularnych odstępach czasu. W polu **częstotliwość** akcji wybierz pozycję **godzina** i w polu **Interwał** wprowadź wartość **1** , aby uruchomić przepływ pracy raz dziennie.

![Akcja cyklu](media/logs-export-logicapp/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Dodaj akcję dzienników Azure Monitor
Kliknij pozycję **+ nowy krok** , aby dodać akcję, która jest uruchamiana po akcji cyklu. W obszarze **Wybierz akcję**wpisz **Azure monitor** , a następnie wybierz pozycję **dzienniki Azure monitor**.

![Akcja dzienników Azure Monitor](media/logs-export-logicapp/select-azure-monitor-connector.png)

Kliknij pozycję **Azure log Analytics — uruchom zapytanie i Wyświetl listę wyników**.

![Zrzut ekranu przedstawiający nową akcję dodawaną do kroku w Projektancie aplikacji logiki. Dzienniki Azure Monitor są wyróżnione w obszarze Wybierz akcję.](media/logs-export-logicapp/select-query-action-list.png)

Zostanie wyświetlony monit o wybranie dzierżawy i przyznanie dostępu do obszaru roboczego Log Analytics przy użyciu konta, które zostanie użyte przez przepływ pracy do uruchomienia zapytania.


## <a name="add-azure-monitor-logs-action"></a>Dodaj akcję dzienników Azure Monitor
Akcja dzienniki Azure Monitor umożliwia określenie zapytania do uruchomienia. Zapytanie dziennika używane w tym przykładzie jest zoptymalizowane pod kątem cyklu godzinowego i zbiera dane pozyskiwane dla określonego czasu wykonania. Na przykład, jeśli przepływ pracy działa o godzinie 4:35, zakres czasu będzie 4:00 do 5:00. Jeśli zmienisz aplikację logiki tak, aby była uruchamiana z inną częstotliwością, konieczna jest również zmiana zapytania. Na przykład jeśli ustawisz cykl do uruchamiania codziennie, ustaw wartość startTime w zapytaniu na startofday (make_datetime (Year, month, Day, 0, 0)). 

Wybierz **subskrypcję** i **grupę zasobów** dla obszaru roboczego log Analytics. Wybierz *log Analytics obszar roboczy* dla **typu zasobu** , a następnie wybierz nazwę obszaru roboczego w obszarze **nazwa zasobu**.

Dodaj następujące zapytanie dziennika do okna **zapytania** .  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**Zakres czasu** określa rekordy, które zostaną uwzględnione w zapytaniu na podstawie kolumny **TimeGenerated** . Należy ustawić wartość równą lub większą niż zakres czasu wybrany w zapytaniu. Ponieważ to zapytanie nie używa kolumny **TimeGenerated** , a następnie **ustawienie opcji zapytania** jest niedostępne. Zobacz [zakres zapytania](../log-query/scope.md) , aby uzyskać więcej szczegółów na temat zakresu czasu. 

Wybierz pozycję **ostatnie 4 godziny** dla **zakresu czasu**. Zapewni to, że wszystkie rekordy o czasie pozyskiwania większym niż **TimeGenerated** zostaną uwzględnione w wynikach.
   
![Zrzut ekranu przedstawiający ustawienia nowej akcji dzienników Azure Monitor o nazwie Run Query i Wizualizuj wyniki.](media/logs-export-logicapp/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Dodaj aktywność analizy JSON (opcjonalnie)
Dane wyjściowe akcji **Run Query i Results list** są sformatowane w formacie JSON. Można przeanalizować te dane i manipulować **nimi w ramach przygotowywania akcji tworzenia** . 

Można podać schemat JSON, który opisuje oczekiwany ładunek. Projektant analizuje zawartość JSON przy użyciu tego schematu i generuje tokeny przyjazne dla użytkownika, które reprezentują właściwości w zawartości JSON. Następnie możesz łatwo odwoływać się do tych właściwości i używać ich w ramach przepływu pracy aplikacji logiki. 


Kliknij pozycję **+ nowy krok**, a następnie kliknij pozycję **+ Dodaj akcję**. W obszarze **Wybierz akcję**wpisz dane **JSON** , a następnie wybierz pozycję **Analizuj dane JSON**.

![Wybieranie działania Przeanalizuj dane JSON](media/logs-export-logicapp/select-parse-json.png)

Kliknij pole **zawartość** , aby wyświetlić listę wartości z poprzednich działań. Wybierz pozycję **treść** z akcji **Uruchom zapytanie i Wyświetl wyniki** . Dane wyjściowe zapytania dziennika.

[![Wybierz treść](media/logs-export-logicapp/select-body.png)](media/logs-export-logicapp/select-body.png#lightbox)

5.  Kliknij pozycję **Użyj przykładowego ładunku do wygenerowania schematu**. Uruchom zapytanie dziennika i skopiuj dane wyjściowe do użycia dla przykładowego ładunku.  Przykładowego zapytania można użyć następujących danych wyjściowych:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Analizowanie ładunku JSON](media/logs-export-logicapp/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Dodaj akcję redagowania
Akcja **redagowania** wykonuje przeanalizowane dane wyjściowe JSON i tworzy obiekt, który należy przechowywać w obiekcie blob.

Kliknij pozycję **+ nowy krok**, a następnie kliknij pozycję **+ Dodaj akcję**. W obszarze **Wybierz akcję**wpisz polecenie **Zredaguj** , a następnie wybierz akcję **Utwórz** .

![Wybierz akcję redagowania](media/logs-export-logicapp/select-compose.png)


Kliknij pole **dane wejściowe** , aby wyświetlić listę wartości z poprzednich działań. Wybierz pozycję **treść** z akcji **Przeanalizuj dane JSON** . To są przeanalizowane dane wyjściowe z zapytania dziennika.

[![Wybierz treść dla akcji redagowania](media/logs-export-logicapp/select-body-compose.png)](media/logs-export-logicapp/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Dodaj akcję tworzenia obiektu BLOB
Akcja Utwórz obiekt BLOB zapisuje utworzony kod JSON w magazynie.

Kliknij pozycję **+ nowy krok**, a następnie kliknij pozycję **+ Dodaj akcję**. W obszarze **Wybierz akcję**wpisz **obiekt BLOB** , a następnie wybierz akcję **Utwórz obiekt BLOB** .

![Wybierz pozycję Utwórz obiekt BLOB](media/logs-export-logicapp/select-create-blob.png)

Wpisz nazwę połączenia z kontem magazynu w polu **Nazwa połączenia** , a następnie kliknij ikonę folderu w obszarze **ścieżka folderu** , aby wybrać kontener na koncie magazynu. Kliknij **nazwę obiektu BLOB** , aby wyświetlić listę wartości z poprzednich działań. Kliknij pozycję **wyrażenie** i wprowadź wyrażenie zgodne z przedziałem czasu. Dla tego zapytania, które jest uruchamiane co godzinę, następujące wyrażenie ustawia nazwę obiektu BLOB na poprzednią godzinę: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Wyrażenie obiektu BLOB](media/logs-export-logicapp/blob-expression.png)](media/logs-export-logicapp/blob-expression.png#lightbox)

Kliknij pole **zawartość obiektu BLOB** , aby wyświetlić listę wartości z poprzednich działań, a następnie wybierz pozycję dane **wyjściowe** w sekcji **redagowanie** .


![Utwórz wyrażenie obiektu BLOB](media/logs-export-logicapp/create-blob.png)


## <a name="test-the-logic-app"></a>Testowanie aplikacji logiki
Przetestuj przepływ pracy, klikając przycisk **Uruchom**. Jeśli przepływ pracy zawiera błędy, zostanie on wskazany w kroku z problemem. Aby wyświetlić dane wejściowe i wyjściowe w celu zbadania błędów, można wyświetlić wykonania i przejść do szczegółów. Zobacz [Rozwiązywanie problemów i Diagnozowanie błędów przepływu pracy w Azure Logic Apps w](../../logic-apps/logic-apps-diagnosing-failures.md) razie potrzeby.

[![Historia przebiegów](media/logs-export-logicapp/runs-history.png)](media/logs-export-logicapp/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Wyświetlanie dzienników w magazynie
Przejdź do menu **konta magazynu** w Azure Portal a następnie wybierz swoje konto magazynu. Kliknij kafelek **obiekty blob** i wybierz kontener określony w akcji Utwórz obiekt BLOB. Wybierz jeden z obiektów blob, a następnie **Edytuj obiekt BLOB**.

[![Dane obiektów BLOB](media/logs-export-logicapp/blob-data.png)](media/logs-export-logicapp/blob-data.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zapytań dzienników w Azure monitor](../log-query/log-query-overview.md).
- Dowiedz się więcej o [Logic Apps](../../logic-apps/index.yml)
- Dowiedz się więcej o [automatyzacji](https://flow.microsoft.com).
