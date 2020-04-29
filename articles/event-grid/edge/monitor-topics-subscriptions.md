---
title: Tematy dotyczące monitorowania i subskrypcje zdarzeń — Azure Event Grid IoT Edge | Microsoft Docs
description: Monitorowanie tematów i subskrypcji zdarzeń
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77086681"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Monitorowanie tematów i subskrypcji zdarzeń

Event Grid na platformie Edge udostępnia wiele metryk dla tematów i subskrypcji zdarzeń w [formacie specyfikacji Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). W tym artykule opisano dostępne metryki i sposób ich włączania.

## <a name="enable-metrics"></a>Włącz metryki

Skonfiguruj moduł do emisji metryk przez ustawienie zmiennej `metrics__reporterType` środowiskowej na `prometheus` w opcjach tworzenia kontenera:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Metryki będą dostępne w `5888/metrics` module dla protokołów HTTP i `4438/metrics` https. Na przykład `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` w przypadku protokołu HTTP. W tym momencie moduł metryk może sondować punkt końcowy, aby zbierać metryki jak w tej [przykładowej architekturze](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Dostępne metryki

Oba tematy i subskrypcje zdarzeń emitują metryki, aby uzyskać wgląd w informacje dotyczące dostarczania zdarzeń i wydajności modułów.

### <a name="topic-metrics"></a>Metryki tematu

| Metryka | Opis |
| ------ | ----------- |
| EventsReceived | Liczba zdarzeń opublikowanych w temacie
| UnmatchedEvents | Liczba zdarzeń opublikowanych w temacie, które nie pasują do subskrypcji zdarzeń i zostały usunięte
| SuccessRequests | Liczba przychodzących żądań publikacji odebranych przez temat
| SystemErrorRequests | Liczba przychodzących żądań publikowania nie powiodła się z powodu wewnętrznego błędu systemu
| UserErrorRequests | Liczba w przychodzących żądaniach publikowania nie powiodła się z powodu błędu użytkownika, takiego jak źle sformułowany kod JSON
| SuccessRequestLatencyMs | Opóźnienie żądania opublikowania odpowiedzi w milisekundach


### <a name="event-subscription-metrics"></a>Metryki subskrypcji zdarzeń

| Metryka | Opis |
| ------ | ----------- |
| DeliverySuccessCounts | Liczba zdarzeń pomyślnie dostarczonych do skonfigurowanego punktu końcowego
| DeliveryFailureCounts | Liczba zdarzeń, które nie zostały dostarczone do skonfigurowanego punktu końcowego
| DeliverySuccessLatencyMs | Opóźnienie zdarzeń zakończonych powodzeniem w milisekundach
| DeliveryFailureLatencyMs | Opóźnienie niepowodzeń dostarczania zdarzeń w milisekundach
| SystemDelayForFirstAttemptMs | Opóźnienie zdarzeń systemu przed pierwszą próbą dostarczenia w milisekundach
| DeliveryAttemptsCount | Liczba prób dostarczenia zdarzenia — sukces i niepowodzenie
| ExpiredCounts | Liczba zdarzeń, które wygasły i nie zostały dostarczone do skonfigurowanego punktu końcowego