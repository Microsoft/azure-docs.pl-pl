---
title: Azure Event Grid dostarczania i ponów próbę
description: Opisuje, w jaki sposób Azure Event Grid dostarcza zdarzenia i jak obsługuje niedostarczone komunikaty.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 924abaa1e5c12c4477bddf888541e7414b7bdbec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324097"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid dostarczania komunikatów i ponów próbę

W tym artykule opisano, jak Azure Event Grid obsługuje zdarzenia, gdy dostarczenie nie jest potwierdzone.

Event Grid zapewnia trwałe dostarczanie. Każdy z nich dostarcza każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są natychmiast wysyłane do zarejestrowanego punktu końcowego dla każdej subskrypcji. Jeśli punkt końcowy nie potwierdzi przyjęcia zdarzenia, Event Grid ponawianie prób dostarczenia zdarzenia.

## <a name="batched-event-delivery"></a>Wsadowe dostarczanie zdarzeń

Event Grid domyślnie wysyłać każde zdarzenie do subskrybentów. Subskrybent otrzymuje tablicę z pojedynczym zdarzeniem. Można skonfigurować Event Grid do zdarzeń wsadowych na potrzeby dostarczania w celu zwiększenia wydajności HTTP w scenariuszach o wysokiej przepływności.

Wsadowe dostarczanie ma dwa ustawienia:

* **Maksymalna Event Grid liczba zdarzeń przypadających na partie** Ta liczba nigdy nie zostanie przekroczona. można jednak dostarczyć mniejszą liczbę zdarzeń, jeśli podczas publikowania nie będą dostępne żadne inne zdarzenia. Event Grid nie opóźnia zdarzeń, aby można było utworzyć partię, jeśli są dostępne mniej zdarzeń. Musi zawierać się w przedziale od 1 do 5 000.
* **Preferowany rozmiar wsadu w kilobajtach** — docelowy limit rozmiaru partii w kilobajtach. Podobnie jak w przypadku maksymalnych zdarzeń, rozmiar wsadu może być mniejszy, jeśli podczas publikowania nie są dostępne więcej zdarzeń. Istnieje możliwość, że partia jest większa niż preferowany rozmiar partii, *Jeśli* pojedyncze zdarzenie jest większe niż preferowany rozmiar. Na przykład jeśli preferowany rozmiar to 4 KB, a do Event Grid zostanie wypchnięte zdarzenie o rozmiarze 10 KB, zdarzenie o rozmiarze 10 KB będzie nadal dostarczane do własnej partii, a nie do porzucenia.

Dostarczanie wsadowe skonfigurowane dla każdej subskrypcji zdarzenia za pośrednictwem portalu, interfejsu wiersza polecenia, programu PowerShell lub zestawów SDK.

### <a name="azure-portal"></a>Azure Portal: 
![Ustawienia dostarczania wsadowego](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas tworzenia subskrypcji zdarzeń należy użyć następujących parametrów: 

- **Maksymalna liczba zdarzeń na partie** z maksymalną liczbą zdarzeń w partii. Musi być liczbą z przedziału od 1 do 5000.
- **preferowany rozmiar partii (w** kilobajtach) — rozmiar wsadu preferowany w kilobajtach. Musi być liczbą z przedziału od 1 do 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure z Event Grid, zobacz [Route Storage Events to Web Endpoint with Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Harmonogram ponownych prób i czas trwania

Event Grid czeka 30 sekund na odpowiedź po dostarczeniu komunikatu. Po 30 sekundach, jeśli punkt końcowy nie odpowiedział, komunikat zostanie umieszczony w kolejce w celu ponowienia próby. W przypadku dostarczania zdarzeń Event Grid są stosowane wykładnicze zasady ponawiania wycofywania. Event Grid ponawianie prób dostarczenia zgodnie z poniższym harmonogramem w oparciu o najlepszą pracę:

- 10 sekund
- 30 sekund
- 1 minuta
- 5 min
- 10 minut
- 30 minut
- 1 godzina
- Co godzinę przez maksymalnie 24 godziny

Jeśli punkt końcowy odpowie w ciągu 3 minut, Event Grid podejmie próbę usunięcia zdarzenia z kolejki ponawiania prób, ale duplikaty mogą nadal zostać odebrane.

Event Grid dodaje małe losowe instrukcje do wszystkich ponownych prób i mogą odpowiednio Uzgodnij pominąć pewne ponowne próby, jeśli punkt końcowy jest ciągle w złej kondycji, nie działa przez długi czas lub wydaje się być przeciążony.

W przypadku zachowań deterministycznych Ustaw czas wygaśnięcia i maksymalną liczbę prób dostarczenia w [zasadach ponawiania subskrypcji](manage-event-delivery.md).

Domyślnie Event Grid wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. [Zasady ponawiania można dostosować](manage-event-delivery.md) podczas tworzenia subskrypcji zdarzeń. Podajesz maksymalną liczbę prób dostarczenia (wartość domyślna to 30) i czas wygaśnięcia zdarzenia na żywo (wartość domyślna to 1440 minut).

## <a name="delayed-delivery"></a>Opóźnione dostarczanie

W przypadku niepowodzeń dostarczania punktów końcowych Event Grid zacznie opóźniać dostarczenie i ponawianie zdarzeń do tego punktu końcowego. Na przykład jeśli pierwsze 10 zdarzeń opublikowanych w punkcie końcowym zakończy się niepowodzeniem, Event Grid przyjmie, że punkt końcowy ma problemy i spowoduje to opóźnienie wszystkich kolejnych ponownych prób *i nowych* dostaw przez pewien czas do kilku godzin.

Celem opóźnionej dostawy jest ochrona punktów końcowych w złej kondycji oraz systemu Event Grid. Bez wycofywania i opóźnień dostarczania w przypadku punktów końcowych w złej kondycji, Event Grid zasady ponawiania prób i możliwości woluminów mogą łatwo zapychać system.

## <a name="dead-letter-events"></a>Zdarzenia utraconych wiadomości
Gdy Event Grid nie może dostarczyć zdarzenia w określonym czasie lub po próbie dostarczenia zdarzenia przez określoną liczbę razy, może wysłać niedostarczone zdarzenie do konta magazynu. Ten proces jest znany jako **utracony**. Event Grid martwych liter zdarzenia po spełnieniu **jednego z następujących** warunków. 

- Zdarzenie nie jest dostarczane w okresie czasu wygaśnięcia
- Liczba prób dostarczenia zdarzenia przekroczyła limit.

Jeśli spełniony jest dowolny z warunków, zdarzenie zostanie porzucone lub utracone.  Domyślnie Event Grid nie powoduje wyłączenia utraconych wiadomości. Aby je włączyć, należy określić konto magazynu do przechowywania niedostarczonych zdarzeń podczas tworzenia subskrypcji zdarzeń. Zdarzenia z tego konta magazynu są ściągane, aby można było rozpoznać dostawy.

Event Grid wysyła zdarzenie do lokalizacji utraconych, gdy nastąpi próba wszystkich ponownych prób. Jeśli Event Grid otrzymuje kod odpowiedzi 400 (złe żądanie) lub 413 (żądanie jest zbyt duże), natychmiast wysyła zdarzenie do punktu końcowego utraconych wiadomości. Te kody odpowiedzi wskazują, że dostarczenie zdarzenia nigdy nie powiedzie się.

Istnieje pięć minut opóźnienia między ostatnią próbą dostarczenia zdarzenia a jego dostarczeniem do lokalizacji utraconych wiadomości. To opóźnienie jest przeznaczone do zmniejszenia liczby operacji magazynu obiektów BLOB. Jeśli lokalizacja utraconych wiadomości jest niedostępna przez cztery godziny, zdarzenie zostanie odrzucone.

Przed ustawieniem lokalizacji utraconych wiadomości musisz mieć konto magazynu z kontenerem. Należy podać punkt końcowy dla tego kontenera podczas tworzenia subskrypcji zdarzeń. Punkt końcowy ma format: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Możesz chcieć otrzymywać powiadomienia, gdy zdarzenie zostało wysłane do lokalizacji utraconych wiadomości. Aby użyć Event Grid do odpowiadania na zdarzenia niedostarczone, [Utwórz subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) dla utraconego magazynu obiektów BLOB. Za każdym razem, gdy magazyn obiektów BLOB utraconych wiadomości odbiera zdarzenie niedostarczone, Event Grid powiadamia program obsługi. Program obsługi reaguje na akcje, które mają zostać wykonane w celu uzgodnienia niedostarczonych zdarzeń. Aby zapoznać się z przykładem konfigurowania martwej lokalizacji i zasad ponawiania, zobacz [utracony komunikat i zasady ponawiania](manage-event-delivery.md).

## <a name="delivery-event-formats"></a>Formaty zdarzeń dostarczania
W tej sekcji przedstawiono przykłady zdarzeń i zdarzeń utraconych w różnych formatach schematów dostarczania (schemat Event Grid, schemat CloudEvents 1,0 i schemat niestandardowy). Aby uzyskać więcej informacji na temat tych formatów, zobacz artykuł [Event Grid schematów](event-schema.md) i [zdarzeń Cloud Events 1,0](cloud-event-schema.md) . 

### <a name="event-grid-schema"></a>Schemat usługi Event Grid

#### <a name="event"></a>Wydarzenie 
```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/000000000-0000-0000-0000-00000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    }
}
```

#### <a name="dead-letter-event"></a>Wydarzenie utraconych wiadomości

```json
{
    "id": "93902694-901e-008f-6f95-7153a806873c",
    "eventTime": "2020-08-13T17:18:13.1647262Z",
    "eventType": "Microsoft.Storage.BlobCreated",
    "dataVersion": "",
    "metadataVersion": "1",
    "topic": "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.Storage/storageAccounts/myegteststgfoo",
    "subject": "/blobServices/default/containers/deadletter/blobs/myBlobFile.txt",    
    "data": {
        "api": "PutBlob",
        "clientRequestId": "c0d879ad-88c8-4bbe-8774-d65888dc2038",
        "requestId": "93902694-901e-008f-6f95-7153a8000000",
        "eTag": "0x8D83FACDC0C3402",
        "contentType": "text/plain",
        "contentLength": 0,
        "blobType": "BlockBlob",
        "url": "https://myegteststgfoo.blob.core.windows.net/deadletter/myBlobFile.txt",
        "sequencer": "00000000000000000000000000015508000000000005101c",
        "storageDiagnostics": { "batchId": "cfb32f79-3006-0010-0095-711faa000000" }
    },

    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T17:18:14.0265758Z",
    "lastDeliveryAttemptTime": "2020-08-13T17:18:14.0465788Z" 
}
```

### <a name="cloudevents-10-schema"></a>Schemat CloudEvents 1,0

#### <a name="event"></a>Wydarzenie

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    }
}
```

#### <a name="dead-letter-event"></a>Wydarzenie utraconych wiadomości

```json
{
    "id": "caee971c-3ca0-4254-8f99-1395b394588e",
    "source": "mysource",
    "dataversion": "1.0",
    "subject": "mySubject",
    "type": "fooEventType",
    "datacontenttype": "application/json",
    "data": {
        "prop1": "value1",
        "prop2": 5
    },

    "deadletterreason": "MaxDeliveryAttemptsExceeded",
    "deliveryattempts": 1,
    "lastdeliveryoutcome": "NotFound",
    "publishtime": "2020-08-13T21:21:36.4018726Z",
}
```

### <a name="custom-schema"></a>Schemat niestandardowy

#### <a name="event"></a>Wydarzenie

```json
{
    "prop1": "my property",
    "prop2": 5,
    "myEventType": "fooEventType"
}

```

#### <a name="dead-letter-event"></a>Wydarzenie utraconych wiadomości
```json
{
    "id": "8bc07e6f-0885-4729-90e4-7c3f052bd754",
    "eventTime": "2020-08-13T18:11:29.4121391Z",
    "eventType": "myEventType",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/00000000000-0000-0000-0000-000000000000000/resourceGroups/rgwithoutpolicy/providers/Microsoft.EventGrid/topics/myCustomSchemaTopic",
    "subject": "subjectDefault",
  
    "deadLetterReason": "MaxDeliveryAttemptsExceeded",
    "deliveryAttempts": 1,
    "lastDeliveryOutcome": "NotFound",
    "publishTime": "2020-08-13T18:11:29.4121391Z",
    "lastDeliveryAttemptTime": "2020-08-13T18:11:29.4277644Z",
  
    "data": {
        "prop1": "my property",
        "prop2": 5,
        "myEventType": "fooEventType"
    }
}
```


## <a name="message-delivery-status"></a>Stan dostarczania wiadomości

Event Grid używa kodów odpowiedzi HTTP w celu potwierdzenia odbioru zdarzeń. 

### <a name="success-codes"></a>Kody sukcesu

Event Grid traktuje **tylko** następujące kody odpowiedzi HTTP jako pomyślne dostawy. Wszystkie inne kody stanu są uważane za nieudane dostawy i zostaną ponowione lub deadlettered zgodnie z potrzebami. Po otrzymaniu kodu stanu pomyślnego Event Grid uznaje, że dostarczenie zostało zakończone.

- 200 OK
- 201 Utworzono
- 202 zaakceptowane
- 203 informacje nieautorytatywne
- 204 Brak zawartości

### <a name="failure-codes"></a>Kody błędów

Wszystkie inne kody, które nie znajdują się w powyższym zestawie (200-204), są uznawane za niepowodzenia i zostanie ponowione. Niektóre z nich mają określone zasady ponawiania prób powiązane z nimi, które podano poniżej, wszystkie inne przestrzegają standardowego modelu wykładniczego wycofywania. Należy pamiętać, że ze względu na wysoce parallelowy charakter architektury Event Grid, zachowanie ponowienia nie jest deterministyczne. 

| Kod stanu | Zachowanie przy ponowieniu próby |
| ------------|----------------|
| 400 Nieprawidłowe żądanie | Ponów próbę po 5 minutach lub więcej (Utracono wiadomości natychmiast, jeśli konfiguracja utraconych wiadomości) |
| 401 Brak autoryzacji | Ponów próbę po 5 minutach lub dłużej |
| 403 Zabronione | Ponów próbę po 5 minutach lub dłużej |
| 404 — Nie znaleziono | Ponów próbę po 5 minutach lub dłużej |
| 408 — limit czasu żądania | Ponów próbę po 2 minutach lub więcej |
| Jednostka żądania 413 jest zbyt duża | Ponów próbę po upływie 10 sekund lub więcej (utraconych wiadomości, jeśli konfiguracja utraconych wiadomości) |
| 503 — usługa niedostępna | Ponów próbę po 30 sekundach lub więcej |
| Wszystkie pozostałe | Ponów próbę po 10 sekundach lub więcej |


## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić stan dostaw zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby dostosować opcje dostarczania zdarzeń, zobacz artykuł [utracony i zasady ponawiania](manage-event-delivery.md).
