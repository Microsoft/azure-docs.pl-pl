---
title: Informacje o zadaniach usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — planowanie zadań do uruchomienia na wielu urządzeniach podłączonych do centrum IoT Hub. Zadania umożliwiają aktualizowanie tagów i żądanych właściwości oraz wywoływanie metod bezpośrednich na wielu urządzeniach.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81730107"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Planowanie zadań na wielu urządzeniach

Platforma Azure IoT Hub umożliwia szereg bloków konstrukcyjnych, takich jak [właściwości i Tagi](iot-hub-devguide-device-twins.md) i [metody bezpośrednie](iot-hub-devguide-direct-methods.md). Zazwyczaj aplikacje zaplecza umożliwiają administratorom i operatorom urządzeń aktualizowanie i pracę z urządzeniami IoT luzem oraz w zaplanowanym czasie. Zadania są wykonywane w zaplanowanym czasie za pomocą urządzeń i metod bezpośrednich względem zestawu urządzeń. Na przykład operator użyje aplikacji zaplecza, która inicjuje i śledzi zadanie w celu ponownego uruchomienia zestawu urządzeń w przypadku budowania 43 i podłogi 3 w czasie, który nie będzie zakłócać operacji konstrukcyjnych.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Należy rozważyć użycie zadań, gdy zachodzi potrzeba zaplanowania i śledzenia postępu jednego z następujących działań na zestawie urządzeń:

* Aktualizowanie żądanych właściwości
* Aktualizowanie tagów
* Wywoływanie metod bezpośrednich

## <a name="job-lifecycle"></a>Cykl życia zadania

Zadania są inicjowane przez zaplecze rozwiązania i obsługiwane przez IoT Hub. Zadanie można zainicjować za pomocą identyfikatora URI `PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30` dotyczącego usługi () i wykonać zapytanie o postęp wykonywania zadania za pomocą identyfikatora URI mającego dostęp do usługi `GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30` (). Aby odświeżyć stan uruchomionych zadań po zainicjowaniu zadania, uruchom zapytanie o zadanie.

> [!NOTE]
> Po zainicjowaniu zadania nazwy i wartości właściwości mogą zawierać tylko znaki alfanumeryczne drukowalne US-ASCII, z wyjątkiem któregokolwiek z następujących zestawów: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Zadania do wykonywania metod bezpośrednich

Poniższy fragment kodu przedstawia szczegóły żądania protokołu HTTPS 1,1 dotyczące wykonywania [bezpośredniej metody](iot-hub-devguide-direct-methods.md) na zestawie urządzeń przy użyciu zadania:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Warunek zapytania może również znajdować się na pojedynczym IDENTYFIKATORze urządzenia lub na liście identyfikatorów urządzeń, jak pokazano w następujących przykładach:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Język zapytań IoT Hub](iot-hub-devguide-query-language.md) obejmuje więcej szczegółów IoT Hub języka zapytań.

Poniższy fragment kodu przedstawia żądanie i odpowiedź dla zadania zaplanowanego do wywołania metody bezpośredniej o nazwie testMethod na wszystkich urządzeniach w usłudze contoso-Hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Zadania do aktualizowania właściwości sznurka urządzenia

Poniższy fragment kodu przedstawia szczegóły żądania protokołu HTTPS 1,1 dotyczące aktualizowania właściwości przędzy urządzenia przy użyciu zadania:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Właściwość *updateTwin* wymaga prawidłowego dopasowania ETag; na przykład `etag="*"` .

Poniższy fragment kodu przedstawia żądanie i odpowiedź dla zadania zaplanowanego do aktualizacji właściwości sznurka urządzenia dla urządzenia test-Hub

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Wykonywanie zapytań o postęp zadań

Poniższy fragment kodu przedstawia szczegóły żądania protokołu HTTPS 1,1 na potrzeby wykonywania zapytań dotyczących zadań:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

ContinuationToken jest dostarczana z odpowiedzi.

Można wykonać zapytanie o stan wykonania zadania na każdym urządzeniu przy użyciu [języka zapytań IoT Hub dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Właściwości zadań

Na poniższej liście przedstawiono właściwości i odpowiadające jej opisy, które mogą być używane podczas wykonywania zapytań dotyczących zadań lub wyników zadań.

| Właściwość | Opis |
| --- | --- |
| **Zadanie** |Dostarczona przez aplikację identyfikator zadania. |
| **Rozpoczęcia** |Godzina rozpoczęcia w aplikacji (ISO-8601) dla tego zadania. |
| **endTime** |Data podanej IoT Hub (ISO-8601) dla momentu ukończenia zadania. Prawidłowe tylko wtedy, gdy zadanie osiągnie stan "ukończone". |
| **Wprowadź** |Typy zadań: |
| | **scheduleUpdateTwin**: zadanie służące do aktualizowania zestawu żądanych właściwości lub tagów. |
| | **scheduleDeviceMethod**: zadanie używane do wywołania metody urządzenia w zestawie bliźniaczych reprezentacji urządzeń. |
| **Stany** |Bieżący stan zadania. Możliwe wartości dla stanu: |
| | **oczekiwanie**: zaplanowana i oczekująca na pobranie przez usługę zadań. |
| | **zaplanowane**: zaplanowano na czas w przyszłości. |
| | **uruchomione**: aktualnie aktywne zadanie. |
| | **anulowano**: zadanie zostało anulowane. |
| | **Niepowodzenie**: zadanie nie powiodło się. |
| | **ukończono**: zadanie zostało ukończone. |
| **deviceJobStatistics** |Statystyka wykonywania zadania. |
| | **deviceJobStatistics** właściwości: |
| | **deviceJobStatistics. liczba urządzeń**: liczba urządzeń w zadaniu. |
| | **deviceJobStatistics. failedCount**: liczba urządzeń, na których zadanie zakończyło się niepowodzeniem. |
| | **deviceJobStatistics. succeededCount**: liczba urządzeń, na których zadanie zakończyło się pomyślnie. |
| | **deviceJobStatistics. runningCount**: liczba urządzeń, na których jest aktualnie uruchomione zadanie. |
| | **deviceJobStatistics. pendingCount**: liczba urządzeń oczekujących na uruchomienie zadania. |

### <a name="additional-reference-material"></a>Dodatkowe materiały referencyjne

Inne tematy referencyjne w przewodniku dewelopera IoT Hub obejmują:

* [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) opisują różne punkty końcowe, które są uwidaczniane przez każde Centrum IoT Hub na potrzeby operacji w czasie wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisują przydziały, które mają zastosowanie do usługi IoT Hub oraz zachowanie ograniczania, które należy oczekiwać podczas korzystania z usługi.

* [Zestawy SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md) wymieniają różne zestawy SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współpracują z usługą IoT Hub.

* [IoT Hub język zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów](iot-hub-devguide-query-language.md) zawiera opis języka zapytań IoT Hub. Użyj tego języka zapytań, aby pobrać informacje z IoT Hub o urządzeniach i zadaniach bliźniaczych reprezentacji.

* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi IoT Hub protokołu MQTT.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować niektóre koncepcje opisane w tym artykule, zobacz następujący samouczek IoT Hub:

* [Planowanie i emitowanie zadań](iot-hub-node-node-schedule-jobs.md)