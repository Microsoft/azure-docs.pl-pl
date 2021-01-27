---
author: baanders
description: plik dołączania dla opcji filtru tras usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98859538"
---
| Nazwa filtru | Opis | Filtruj schemat tekstu | Obsługiwane wartości | 
| --- | --- | --- | --- |
| PRAWDA/FAŁSZ | Umożliwia tworzenie trasy bez filtrowania lub wyłączanie trasy, tak aby żadne zdarzenia nie były wysyłane | `<true/false>` | `true` = Trasa jest włączona bez filtrowania <br> `false` = Trasa jest wyłączona |
| Typ | [Typ zdarzenia](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) przepływającego przez swoje cyfrowe wystąpienie sieci dwuosiowej | `type = '<eventType>'` | Oto możliwe wartości typów zdarzeń: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Element źródłowy | Nazwa wystąpienia usługi Azure Digital bliźniaczych reprezentacji | `source = '<hostname>'`| Oto możliwe wartości nazwy hosta: <br> **Powiadomienia**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Dla telemetrii**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Temat | Opis zdarzenia w kontekście źródła zdarzeń powyżej | `subject = '<subject>'` | Oto możliwe wartości tematu: <br>**Powiadomienia**: podmiot jest `<twinid>` <br> lub format identyfikatora URI dla tematów, które są jednoznacznie identyfikowane przez wiele części lub identyfikatorów:<br>`<twinid>/relationships/<relationshipid>`<br> **W przypadku telemetrii**: podmiot jest ścieżką składnika (Jeśli dane telemetryczne są emitowane z składnika przędzy), np `comp1.comp2` .. Jeśli dane telemetryczne nie są emitowane ze składnika, jego pole podmiotu jest puste. |
| Schemat danych | Identyfikator modelu DTDL | `dataschema = '<model-dtmi-ID>'` | **W przypadku telemetrii**: schemat danych jest identyfikatorem modelu dwuosiowego lub składnikiem, który emituje dane telemetryczne. Na przykład `dtmi:example:com:floor4;2` <br>W **przypadku powiadomień (Tworzenie/usuwanie)**: schemat danych jest dostępny w treści powiadomienia pod adresem `$body.$metadata.$model` . <br>**Powiadomienia (aktualizacja)**: dostęp do schematu danych można uzyskać w treści powiadomienia w `$body.modelId`|
| Typ zawartości | Typ zawartości wartości danych | `datacontenttype = '<contentType>'` | Typ zawartości to `application/json` |
| Wersja specyfikacji | Wersja schematu zdarzeń, którego używasz | `specversion = '<version>'` | Wersja musi być `1.0` . Oznacza to, że schemat CloudEvents w wersji 1,0 |
| Treść powiadomienia | Odwoływanie się do dowolnej właściwości w `data` polu powiadomienia | `$body.<property>` | Zapoznaj się z tematem [*How to: zrozumieć dane zdarzenia*](../articles/digital-twins/how-to-interpret-event-data.md) dotyczące przykładów powiadomień. Do każdej właściwości w `data` polu można odwoływać się za pomocą `$body`

Następujące typy danych są obsługiwane jako wartości zwracane przez odwołania do danych powyżej:

| Typ danych | Przykład |
|-|-|-|
|**Ciąg**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Liczba całkowita**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Logiczna**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Podczas definiowania filtrów trasy obsługiwane są następujące operatory:

|Family|Operatory|Przykład|
|-|-|-|
|Wartości logiczne|AND, OR, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Porównanie|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

Podczas definiowania filtrów trasy obsługiwane są następujące funkcje:

|Funkcja|Opis|Przykład|
|--|--|--|
|STARTS_WITH (x, y)|Zwraca wartość true, jeśli wartość zaczyna się od `x` ciągu `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Zwraca wartość true, jeśli wartość jest `x` kończąca się ciągiem `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|ZAWIERA (x, y)| Zwraca wartość true, jeśli wartość `x` zawiera ciąg `y` .|`CONTAINS(subject, '<twinID>')`|

Po zaimplementowaniu lub zaktualizowaniu filtru zmiana może potrwać kilka minut w potoku danych.