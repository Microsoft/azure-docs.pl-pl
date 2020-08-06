---
author: baanders
description: plik dołączania dla opcji filtru tras usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: f9db7c489bb1c64a1649bba74d08fef5f0ec97b8
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801635"
---
| Nazwa filtru | Opis | Filtruj schemat tekstu | Obsługiwane wartości | 
| --- | --- | --- | --- |
| Typ | [Typ zdarzenia](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) przepływającego przez swoje cyfrowe wystąpienie sieci dwuosiowej | `type = '<eventType>'` | Oto możliwe wartości typów zdarzeń: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Źródło | Nazwa wystąpienia usługi Azure Digital bliźniaczych reprezentacji | `source = '<hostname>'`| Oto możliwe wartości nazwy hosta: <br> **Powiadomienia**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Dla telemetrii**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Temat | Opis zdarzenia w kontekście źródła zdarzeń powyżej | `subject = '<subject>'` | Oto możliwe wartości tematu: <br>**Powiadomienia**: podmiot jest`<twinid>` <br> lub format identyfikatora URI dla tematów, które są jednoznacznie identyfikowane przez wiele części lub identyfikatorów:<br>`<twinid>/relationships/<relationshipid>`<br> **W przypadku telemetrii**: podmiot jest ścieżką składnika (Jeśli dane telemetryczne są emitowane z składnika przędzy), np `comp1.comp2` .. Jeśli dane telemetryczne nie są emitowane ze składnika, jego pole podmiotu jest puste. |
| Schemat danych | Identyfikator modelu DTDL | `dataschema = '<model-dtmi-ID>'` | **W przypadku telemetrii**: schemat danych jest identyfikatorem modelu dwuosiowego lub składnikiem, który emituje dane telemetryczne. Na przykład `dtmi:example:com:floor4;2` <br>**Dla powiadomień**: schemat danych nie jest obsługiwany|
| Typ zawartości | Typ zawartości wartości danych | `datacontenttype = '<contentType>'` | Typ zawartości to`application/json` |
| Wersja specyfikacji | Wersja schematu zdarzeń, którego używasz | `specversion = '<version>'` | Wersja musi być `1.0` . Oznacza to, że schemat CloudEvents w wersji 1,0 |
| PRAWDA/FAŁSZ | Umożliwia tworzenie trasy bez filtrowania lub wyłączanie trasy | `<true/false>` | `true`= Trasa jest włączona bez filtrowania <br> `false`= Trasa jest wyłączona |

Istnieje również możliwość łączenia filtrów przy użyciu następujących operacji:

| Nazwa filtru | Filtruj schemat tekstu | Przykład | 
| --- | --- | --- |
| I/LUB | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| I/LUB | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Operacje zagnieżdżone | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> W trakcie okresu zapoznawczego obsługiwane są tylko ciągi równości (=,! =).

Po zaimplementowaniu lub zaktualizowaniu filtru zmiana może potrwać kilka minut w potoku danych.
