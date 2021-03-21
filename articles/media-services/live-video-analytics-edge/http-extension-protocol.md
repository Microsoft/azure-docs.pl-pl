---
title: Protokół HTTP Extension — Azure
description: Ten artykuł zawiera informacje na temat używania protokołu HTTP Extension do wysyłania komunikatów między modułami analizy wideo na żywo a modułem AI lub OKS.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: c3a9b5b70467479fdb9b1473a1f6738eaa8a7e1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455894"
---
# <a name="http-extension-protocol"></a>Protokół rozszerzenia HTTP

Funkcja analizy filmów wideo na żywo w systemie IoT Edge umożliwia rozszerzanie możliwości przetwarzania wykresów multimedialnych za pomocą [węzła rozszerzenia grafu](/azure/media-services/live-video-analytics-edge/media-graph-extension-concept). Jeśli używasz procesora rozszerzenia HTTP jako węzła rozszerzenia, komunikacja między modułem analizy wideo na żywo a modułem AI lub OKS jest za pośrednictwem protokołu HTTP

Ten artykuł zawiera informacje na temat używania protokołu HTTP Extension do wysyłania komunikatów między modułami analizy wideo na żywo a modułem AI lub OKS. 

Kontrakt HTTP został zdefiniowany między następującymi dwoma składnikami:

* Serwer HTTP
* Analiza filmów wideo na żywo na IoT Edge module działa jako klient HTTP

## <a name="request"></a>Żądanie

Żądania z modułu analizy wideo na żywo do serwera HTTP byłyby następujące:

|Klucz|Wartość|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Zaakceptuj|Application/JSON,  */*|
|Autoryzacja|Basic, Digest, Bearer (za pomocą obsługi niestandardowego nagłówka)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>obraz/x — RAW|
|Długość treści (w bajtach)|
|User-Agent|Azure Media Services|
|Treść|Bajty obrazów, binarne zakodowane w jednym z obsługiwanych typów zawartości.|

### <a name="example"></a>Przykład

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Reakcja

Odpowiedzi z modułu do modułu analizy wideo na żywo powinny być następujące:

|Klucz|Wartość|
|---|---|
|Kody stanu|200 OK — znaleziono wyniki wnioskowania<br/>204 Brak zawartości — nie znaleziono zawartości przez AI<br/>400 Nieprawidłowe żądanie — nieoczekiwane<br/>Błąd wewnętrzny serwera 500 — nieoczekiwany<br/>503 serwer zajęty — AMS zostanie wycofana w oparciu o nagłówek "Ponów próbę po" lub na podstawie domyślnej wielkości czasu w nagłówku przypadku, który nie jest wstępnie ustawiony.|
|Content-Type|application/json|
|Długość zawartości|Długość treści w bajtach|
|Treść|Obiekt JSON z pojedynczą właściwością wnioskowania.|

### <a name="example"></a>Przykład

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Zdecydowanie zaleca się, aby odpowiedzi są zwracane przy użyciu prawidłowych dokumentów JSON, zgodnie ze wstępnie ustanowionym schematem zdefiniowanym zgodnie z [modelem obiektu schematu metadanych wnioskowania](/azure/media-services/live-video-analytics-edge/inference-metadata-schema). Pozwoli to lepiej zapewnić współdziałanie z innymi składnikami i możliwymi przyszłymi możliwościami dodanymi do modułu analizy wideo na żywo.

Jeśli moduł zwróci odpowiedź, gdy typem zawartości nie jest "Application/JSON", analiza wideo na żywo będzie kodować komunikat jako podstawową zawartość 64 i serializować go jako nieprzezroczysty ładunek JSON.

Jeśli moduł zwróci odpowiedź z typem zawartości jako "Application/JSON", ale schemat JSON nie postępuje zgodnie ze schematem metadanych wnioskowania opisanym poniżej, ładunek wiadomości zostanie przekazany przez potok, ale współdziałanie zostanie zredukowane. Zapoznaj się z [tym](/azure/media-services/live-video-analytics-edge/inference-metadata-schema) artykułem, aby zapoznać się ze szczegółowymi informacjami dotyczącymi schematu metadanych wnioskowania.

> [!NOTE]
> Jeśli moduł nie wygenerował żadnego wyniku, powinien zwrócić kod stanu HTTP 204 (brak zawartości) z pustą treścią odpowiedzi. Analiza filmów wideo na żywo będzie zrozumieć ten element jako pusty wynik i nie spowoduje przekazanie zdarzenia w toku.


## <a name="next-steps"></a>Następne kroki

[Protokół rozszerzenia gRPC](./grpc-extension-protocol.md)
