---
title: Opis zadań usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — planowanie zadań do uruchamiania na wielu urządzeniach podłączonych do centrum IoT Hub. Zadania można aktualizować tagi i żądane właściwości i wywoływać metody bezpośrednie na wielu urządzeniach.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730107"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Planowanie zadań na wielu urządzeniach

Usługa Azure IoT Hub umożliwia szereg bloków konstrukcyjnych, takich jak [właściwości bliźniaczej reprezentacji urządzenia i tagi](iot-hub-devguide-device-twins.md) oraz [metody bezpośrednie.](iot-hub-devguide-direct-methods.md) Zazwyczaj aplikacje zaplecza umożliwiają administratorom urządzeń i operatorom aktualizowanie i interakcję z urządzeniami IoT i interakcję z nimi zbiorczo i w zaplanowanym czasie. Zadania wykonują bliźniacze aktualizacje urządzeń i bezpośrednie metody względem zestawu urządzeń w zaplanowanym czasie. Na przykład operator użyje aplikacji zaplecza, która inicjuje i śledzi zadanie, aby ponownie uruchomić zestaw urządzeń w budynku 43 i 3 w czasie, który nie będzie zakłócający działania budynku.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Rozważ użycie zadań, gdy musisz zaplanować i śledzić postęp, aby wykonać dowolne z następujących działań na zestawie urządzeń:

* Aktualizowanie żądanych właściwości
* Aktualizuj tagi
* Wywoływanie metod bezpośrednich

## <a name="job-lifecycle"></a>Cykl życia pracy

Zadania są inicjowane przez zaplecze rozwiązania i obsługiwane przez centrum IoT Hub. Zadanie można zainicjować za pomocą identyfikatora`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`URI skierowanego do usługi ( ) i kwerendy`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`o postęp w wykonywaniu zadania za pośrednictwem identyfikatora URI wychodzącego z usługą ( ). Aby odświeżyć stan uruchomionych zadań po zainicjowaniu zadania, uruchom kwerendę zadania.

> [!NOTE]
> Podczas inicjowania zadania nazwy i wartości właściwości mogą zawierać tylko alfanumeryczne wydruki US-ASCII, z wyjątkiem dowolnego z następujących zestawów:`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Zadania do wykonania metod bezpośrednich

Poniższy fragment kodu zawiera szczegóły żądania HTTPS 1.1 do wykonywania [metody bezpośredniej](iot-hub-devguide-direct-methods.md) na zestawie urządzeń przy użyciu zadania:

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

Warunek kwerendy może również znajdować się na jednym identyfikatorze urządzenia lub na liście identyfikatorów urządzeń, jak pokazano w poniższych przykładach:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[Język zapytań centrum IoT](iot-hub-devguide-query-language.md) zawiera dodatkowe informacje o języku zapytań centrum IoT.

Poniższy fragment kodu pokazuje żądanie i odpowiedź dla zadania zaplanowanego do wywołania metody bezpośredniej o nazwie testMetoda na wszystkich urządzeniach contoso-hub-1:

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

## <a name="jobs-to-update-device-twin-properties"></a>Zadania aktualizacji właściwości bliźniaczej reprezentacji urządzenia

Poniższy fragment kodu zawiera szczegóły żądania HTTPS 1.1 dotyczące aktualizowania właściwości bliźniaczej reprezentacji urządzenia przy użyciu zadania:

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
> Właściwość *updateTwin* wymaga prawidłowego dopasowania etag; na przykład `etag="*"`.

Poniższy fragment kodu zawiera żądanie i odpowiedź dla zadania zaplanowanego do aktualizacji właściwości bliźniaczej reprezentacji urządzenia dla urządzenia testowego na contoso-hub-1:

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

## <a name="querying-for-progress-on-jobs"></a>Wyszukiwanie zapytań o postęp w pracach

Poniższy fragment kodu zawiera szczegóły żądania HTTPS 1.1 do wykonywania zapytań dotyczących zadań:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

KontynuacjaDek jest dostarczany z odpowiedzi.

Można zbadać stan wykonania zadania na każdym urządzeniu przy użyciu [języka zapytań Usługi IoT Hub dla bliźniaczych urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md).

## <a name="jobs-properties"></a>Właściwości oferty pracy

Na poniższej liście przedstawiono właściwości i odpowiednie opisy, które mogą być używane podczas wykonywania zapytań o zadania lub wyniki zadania.

| Właściwość | Opis |
| --- | --- |
| **Jobid** |Podanie aplikacji pod warunkiem identyfikatora zadania. |
| **startTime** |Podanie aplikacji pod warunkiem, że czas rozpoczęcia zadania (ISO-8601). |
| **Endtime** |Centrum IoT podało datę (ISO-8601) dla momentu zakończenia zadania. Ważne dopiero po osiągnięciu stanu "ukończone" zadania. |
| **Typu** |Rodzaje zadań: |
| | **scheduleUpdateTwin**: Zadanie używane do aktualizowania zestawu żądanych właściwości lub tagów. |
| | **scheduleDeviceMethod**: Zadanie używane do wywoływania metody urządzenia na zestawie bliźniąt urządzeń. |
| **Stan** |Bieżący stan zadania. Możliwe wartości stanu: |
| | **oczekujące**: Zaplanowane i oczekujące na odebranie przez służbę pracy. |
| | **zaplanowane**: Zaplanowane na pewien czas w przyszłości. |
| | **działa**: Aktualnie aktywne zadanie. |
| | **anulowane**: Zadanie zostało anulowane. |
| | **nie powiodło się:** Zadanie nie powiodło się. |
| | **ukończono**: Zadanie zostało zakończone. |
| **deviceJobStatistics (opisy urządzeńJobstatystyczne)** |Statystyki dotyczące wykonania zadania. |
| | **właściwości deviceJobStatistics:** |
| | **deviceJobStatistics.deviceCount:** Liczba urządzeń w zadaniu. |
| | **deviceJobStatistics.failedCount:** Liczba urządzeń, na których zadanie nie powiodło się. |
| | **deviceJobStatistics.succeededCount:** Liczba urządzeń, na których zadanie zakończyło się pomyślnie. |
| | **deviceJobStatistics.runningCount:** Liczba urządzeń, na których jest aktualnie uruchomione zadanie. |
| | **deviceJobStatistics.pendingCount:** Liczba urządzeń oczekujących na uruchomienie zadania. |

### <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) opisano różne punkty końcowe, które każdy centrum IoT udostępnia dla operacji wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisuje przydziały, które mają zastosowanie do usługi Centrum IoT i zachowanie ograniczania przepustowości, których można oczekiwać podczas korzystania z usługi.

* [Zestaw SDK urządzeń i usług Usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK w różnych językach, których można używać podczas tworzenia aplikacji na urządzenia i usługi, które współdziałają z centrum IoT Hub.

* [Język zapytań centrum IoT dla bliźniaczych urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) opisuje język zapytań usługi IoT Hub. Ten język zapytań służy do pobierania informacji z Usługi IoT Hub o bliźniaczych urządzeń i zadaniach.

* [Obsługa protokołu MQTT w uężoniać w uiszczaprzestwu IoT Hub](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi protokołu MQTT w centrum IoT Hub.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujący samouczek Usługi IoT Hub:

* [Planowanie i emitowanie zadań](iot-hub-node-node-schedule-jobs.md)