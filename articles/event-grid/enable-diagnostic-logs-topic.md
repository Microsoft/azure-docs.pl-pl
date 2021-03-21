---
title: Azure Event Grid — Włączanie dzienników diagnostycznych dla tematów lub domen
description: Ten artykuł zawiera instrukcje krok po kroku dotyczące włączania dzienników diagnostycznych tematu usługi Azure Event Grid.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576455"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Włączanie dzienników diagnostycznych dla tematów lub domen usługi Azure Event Grid
Ten artykuł zawiera instrukcje krok po kroku dotyczące włączania ustawień diagnostycznych dla Event Grid tematów lub domen.  Te ustawienia umożliwiają przechwytywanie i wyświetlanie dzienników **błędów publikowania i dostarczania** . 

## <a name="prerequisites"></a>Wymagania wstępne

- Temat zainicjowanej siatki zdarzeń
- Obsługiwane miejsce docelowe przechwytywania dzienników diagnostycznych. Może to być jeden z następujących miejsc docelowych w tej samej lokalizacji, w której znajduje się temat siatka zdarzeń:
    - Konto magazynu Azure
    - Centrum zdarzeń
    - Obszar roboczy usługi Log Analytics

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Włączanie dzienników diagnostycznych tematu niestandardowego

> [!NOTE]
> Poniższa procedura zawiera instrukcje krok po kroku dotyczące włączania dzienników diagnostycznych w temacie. Kroki umożliwiające włączenie dzienników diagnostycznych dla domeny są bardzo podobne. W kroku 2 Przejdź do **domeny** w usłudze Event grid w Azure Portal.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do tematu usługi Event Grid, dla którego chcesz włączyć ustawienia dziennika diagnostycznego. 
    1. Na pasku wyszukiwania u góry Szukaj **Event Grid tematy**. 
    
        ![Wyszukiwanie niestandardowych tematów](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Wybierz **temat** z listy, dla którego chcesz skonfigurować ustawienia diagnostyczne. 
1. Wybierz pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie** w menu po lewej stronie.
1. Na stronie **Ustawienia diagnostyczne** wybierz opcję **Dodaj nowe ustawienie diagnostyczne**. 
    
    ![Dodaj przycisk ustawień diagnostycznych](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Określ **nazwę** ustawienia diagnostycznego. 
6. Wybierz opcje **DeliveryFailures** i **PublishFailures** w sekcji **Dziennik** . 
    ![Wybierz błędy](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Włącz co najmniej jeden z lokalizacji docelowych przechwytywania dla dzienników, a następnie skonfiguruj je, wybierając poprzedni utworzony zasób przechwytywania. 
    - Jeśli wybierzesz opcję **Archiwizuj na koncie magazynu**, wybierz pozycję **konto magazynu — konfiguracja**, a następnie wybierz konto magazynu w ramach subskrypcji platformy Azure. 

        ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyczne" z zaznaczonym ustawieniem "Archiwizuj na konto usługi Azure Storage" i wybranym kontem magazynu.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - W przypadku wybrania opcji **strumień do centrum zdarzeń** wybierz pozycję **centrum zdarzeń — Skonfiguruj**, a następnie wybierz Event Hubs przestrzeń nazw, centrum zdarzeń i zasady dostępu. 
        ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyczne" z zaznaczonym komunikatem "strumień do centrum zdarzeń".](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - W przypadku wybrania opcji **Wyślij do log Analytics** wybierz obszar roboczy log Analytics.
        ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyczne" z zaznaczoną opcją "Wyślij do Log Analytics".](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Wybierz pozycję **Zapisz**. Następnie w prawym górnym rogu wybierz pozycję **X** , aby zamknąć stronę. 
9. Teraz wróć na stronę **Ustawienia diagnostyczne** , aby potwierdzić, że w tabeli **ustawień diagnostycznych** jest wyświetlany nowy wpis. 
    ![Zrzut ekranu przedstawiający stronę "Ustawienia diagnostyczne" z nowym wpisem wyróżnionym w tabeli "Ustawienia diagnostyki".](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Możesz również włączyć zbieranie wszystkich metryk dla tematu. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Włączanie dzienników diagnostycznych tematu systemu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do tematu usługi Event Grid, dla którego chcesz włączyć ustawienia dziennika diagnostycznego. 
    1. Na pasku wyszukiwania u góry Wyszukaj **Event Grid tematy systemowe**. 
    
        ![Wyszukaj tematy systemowe](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Wybierz **temat systemowy** , dla którego chcesz skonfigurować ustawienia diagnostyczne. 
    
        ![Wybieranie tematu systemu](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Wybierz pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie** w menu po lewej stronie, a następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**. 

    ![Dodawanie ustawień diagnostycznych — przycisk](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Określ **nazwę** ustawienia diagnostycznego. 
7. Wybierz **DeliveryFailures** w sekcji **log** . 
    ![Wybór niepowodzeń dostarczania](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Włącz co najmniej jeden z lokalizacji docelowych przechwytywania dla dzienników, a następnie skonfiguruj je, wybierając poprzedni utworzony zasób przechwytywania. 
    - W przypadku wybrania opcji **Wyślij do log Analytics** wybierz obszar roboczy log Analytics.
        ![Wysyłanie do usługi Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Jeśli wybierzesz opcję **Archiwizuj na koncie magazynu**, wybierz pozycję **konto magazynu — konfiguracja**, a następnie wybierz konto magazynu w ramach subskrypcji platformy Azure. 

        ![Archiwizowanie na koncie usługi Azure Storage](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - W przypadku wybrania opcji **strumień do centrum zdarzeń** wybierz pozycję **centrum zdarzeń — Skonfiguruj**, a następnie wybierz Event Hubs przestrzeń nazw, centrum zdarzeń i zasady dostępu. 
        ![Przesyłanie strumieniowe do centrum zdarzeń](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Wybierz pozycję **Zapisz**. Następnie w prawym górnym rogu wybierz pozycję **X** , aby zamknąć stronę. 
9. Teraz wróć na stronę **Ustawienia diagnostyczne** , aby potwierdzić, że w tabeli **ustawień diagnostycznych** jest wyświetlany nowy wpis. 
    ![Ustawienie diagnostyczne na liście](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Można również włączyć zbieranie wszystkich **metryk** dla tematu systemowego.

    ![Temat systemu — Włącz wszystkie metryki](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Wyświetlanie dzienników diagnostycznych w usłudze Azure Storage 

1. Po włączeniu konta magazynu jako miejsca docelowego przechwytywania Event Grid rozpocznie emitowanie dzienników diagnostycznych. Powinny być widoczne nowe kontenery o nazwie **Insights-Logs-deliveryfailures** i **Insights-Logs-publishfailures** na koncie magazynu. 

    ![Magazyn — kontenery dla dzienników diagnostycznych](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Podczas poruszania się po jednym z kontenerów można zakończyć korzystanie z obiektu BLOB w formacie JSON. Plik zawiera wpisy dziennika dla niepowodzenia dostarczania lub błędu publikacji. Ścieżka nawigacji reprezentuje identyfikator **zasobu** tematu siatki zdarzeń oraz sygnaturę czasową (poziom minuty), która ma być emitowana przez wpisy dziennika. Plik BLOB/JSON, który jest dostępny do pobrania, w końcu jest zgodny ze schematem opisanym w następnej sekcji. 

    [![Plik JSON w magazynie ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Zawartość powinna zostać wyświetlona w pliku JSON podobnym do poniższego przykładu: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje o schemacie dziennika i innych pojęciach dotyczących dzienników diagnostycznych dla tematów lub domen, zobacz [dzienniki diagnostyczne](diagnostic-logs.md).
