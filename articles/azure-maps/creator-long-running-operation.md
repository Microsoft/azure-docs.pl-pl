---
title: Interfejs API operacji Long-Running Azure Maps
description: Dowiedz się więcej na temat długotrwałego przetwarzania asynchronicznego w tle w Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f5fb7c8059c8b98e8ec514a4159e96f48db7b1ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906203"
---
# <a name="creator-preview-long-running-operation-api"></a>Interfejs API operacji Long-Running Creator (wersja zapoznawcza)

> [!IMPORTANT]
> Usługi Azure Maps Creator Services są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Niektóre interfejsy API w Azure Maps używają [wzorca Request-Reply asynchronicznego](/azure/architecture/patterns/async-request-reply). Ten wzorzec umożliwia Azure Maps zapewniania usług o wysokiej dostępności i odpowiedzi. W tym artykule opisano implementację określonej asynchronicznej operacji przetwarzania w tle długotrwałą przez usługę Azure map.

## <a name="submitting-a-request"></a>Przesyłanie żądania

Aplikacja kliencka uruchamia długotrwałą operację za pośrednictwem synchronicznego wywołania interfejsu API protokołu HTTP. Zwykle to wywołanie jest w postaci żądania HTTP POST. Po pomyślnym utworzeniu obciążenia asynchronicznego interfejs API zwróci `202` kod stanu HTTP, wskazując, że żądanie zostało zaakceptowane. Ta odpowiedź zawiera `Location` nagłówek wskazujący punkt końcowy, który może być sondowany przez klienta w celu sprawdzenia stanu długotrwałej operacji.

### <a name="example-of-a-success-response"></a>Przykład odpowiedzi sukcesu

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Jeśli wywołanie nie przeszedł pomyślnie walidacji, interfejs API zamiast tego zwróci `400` odpowiedź HTTP dla nieprawidłowego żądania. Treść odpowiedzi zapewni klientowi więcej informacji na temat tego, dlaczego żądanie było nieprawidłowe.

### <a name="monitoring-the-operation-status"></a>Monitorowanie stanu operacji

Punkt końcowy lokalizacji podany w nagłówkach odpowiedzi zaakceptowanych może być sondowany w celu sprawdzenia stanu długotrwałej operacji. Treść odpowiedzi z żądania stanu operacji zawsze będzie zawierać `status` `createdDateTime` właściwości i. `status`Właściwość pokazuje bieżący stan długotrwałej operacji. Możliwe stany obejmują `"NotStarted"` , `"Running"` , `"Succeeded"` , i `"Failed"` . `createdDateTime`Właściwość pokazuje czas rozpoczęcia długotrwałego żądania, który ma zostać uruchomiony. Gdy stan ma wartość `"NotStarted"` lub `"Running"` , `Retry-After` zostanie również dostarczony nagłówek z odpowiedzią. `Retry-After`Nagłówek (w sekundach) może służyć do określenia, kiedy powinno zostać wykonane następne wywołanie sondowania do interfejsu API stanu operacji.

### <a name="example-of-running-a-status-response"></a>Przykład uruchamiania odpowiedzi o stanie

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Zapełnianie operacji obsługi

Po zakończeniu długotrwałej operacji stan odpowiedzi będzie albo `"Succeeded"` `"Failed"` . Po utworzeniu nowego zasobu na podstawie długotrwałej operacji odpowiedź o powodzeniu zwróci `201 Created` kod stanu HTTP. Odpowiedź będzie również zawierać nagłówek wskazujący `Location` metadane dotyczące zasobu. Gdy nie został utworzony nowy zasób, odpowiedź o powodzeniu zwróci `200 OK` kod stanu HTTP. Po wystąpieniu błędu kod stanu odpowiedzi będzie również `200 OK` kodem. Jednak odpowiedź będzie miała `error` Właściwość w treści. Dane błędu są zgodne ze specyfikacją błędów OData.

### <a name="example-of-success-response"></a>Przykład odpowiedzi sukcesu

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Przykład odpowiedzi na awarie

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```