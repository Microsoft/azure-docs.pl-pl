---
title: Pamięć podręczna platformy Azure dla Redis jako źródło Event Grid
description: Opisuje właściwości dostępne dla usługi Azure cache dla zdarzeń Redis z Azure Event Grid
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055973"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Pamięć podręczna platformy Azure dla Redis jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat usługi Azure cache dla zdarzeń Redis. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). 

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Lista zdarzeń dla usługi Azure cache for Redis interfejsy API REST

Te zdarzenia są wyzwalane, gdy klient eksportuje, importuje lub skaluje, wywołując usługę Azure cache for Redis interfejsy API REST. Zdarzenie stosowania poprawki jest wyzwalane przez aktualizację Redis.

 |Nazwa zdarzenia |Opis|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |Wyzwalane po wyeksportowaniu danych pamięci podręcznej. |
 |**Microsoft. cache. ImportRDBCompleted** |Wyzwalane po zaimportowaniu danych pamięci podręcznej. |
 |**Microsoft. cache. PatchingCompleted** |Wyzwalane po zakończeniu stosowania poprawek. |
 |**Microsoft. cache. ScalingCompleted** |Wyzwalane po zakończeniu skalowania. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Zawartość odpowiedzi na zdarzenie

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego.

Ta sekcja zawiera przykład sposobu, w jaki będą wyglądać dane dla każdego zdarzenia usługi Azure cache for Redis.

### <a name="microsoftcachepatchingcompleted-event"></a>Zdarzenie Microsoft. cache. PatchingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Zdarzenie Microsoft. cache. ImportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Zdarzenie Microsoft. cache. ExportRDBCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Pamięć podręczna platformy Azure dla danych zdarzeń Redis. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| sygnatura czasowa | ciąg | Godzina wystąpienia zdarzenia. |
| name | ciąg | Nazwa zdarzenia. |
| status | ciąg | Stan zdarzenia. Nie powiodło się lub zakończyło się pomyślnie. |


## <a name="quickstarts"></a>Przewodniki Szybki start

Jeśli chcesz wypróbować usługę Azure cache dla zdarzeń Redis, zobacz dowolny z tych artykułów szybkiego startu:

|Jeśli chcesz użyć tego narzędzia:    |Zobacz ten artykuł: |
|--|-|
|Witryna Azure Portal    |[Szybki Start: kierowanie pamięci podręcznej platformy Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu Azure Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Szybki Start: kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu programu PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Interfejs wiersza polecenia platformy Azure    |[Szybki Start: kierowanie pamięci podręcznej Azure dla zdarzeń Redis do punktu końcowego sieci Web przy użyciu interfejsu wiersza polecenia](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).

