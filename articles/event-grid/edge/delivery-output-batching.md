---
title: Tworzenie wsadowe danych wyjściowych w Azure Event Grid IoT Edge | Microsoft Docs
description: Tworzenie wsadowe danych wyjściowych w Event Grid na IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171639"
---
# <a name="output-batching"></a>Dzielenie na partie danych wyjściowych

Event Grid obsługuje dostarczanie więcej niż jednego zdarzenia w jednym żądaniu dostarczania. Ta funkcja umożliwia zwiększenie ogólnej przepływności dostarczania bez płacenia kosztów ogólnych na żądanie HTTP. Przetwarzanie wsadowe jest domyślnie wyłączone i można je włączyć dla każdej subskrypcji.

> [!WARNING]
> Maksymalny dozwolony czas trwania przetwarzania każdego żądania dostarczania nie zmienia się, nawet jeśli kod subskrybenta może wykonać więcej pracy na żądanie wsadowe. Wartość domyślna limitu czasu dostarczania na 60 sekund.

## <a name="batching-policy"></a>Zasady wsadowe

Zachowanie wsadowe Event Grid można dostosować na subskrybencie, dostosowując następujące dwa ustawienia:

* Maksymalna liczba zdarzeń na partię

  To ustawienie ustawia górny limit liczby zdarzeń, które można dodać do żądania dostarczania wsadowego.

* Preferowany rozmiar wsadu w kilobajtach

  To pokrętło służy do dalszej kontroli maksymalnej liczby kilobajtów, które mogą być wysyłane na żądanie dostarczania

## <a name="batching-behavior"></a>Działanie przetwarzania wsadowego

* Wszystkie lub brak

  Event Grid działa z semantyką All-lub-None. Nie obsługuje częściowej sukcesu dostarczania wsadowego. Subskrybenci powinni mieć szczególną ostrożność, aby poprosił o tyle zdarzeń na partię, ile mogą one być w rozsądny sposób obsługiwane przez 60 sekund.

* Optymistyczne przetwarzanie wsadowe

  Ustawienia wsadowe zasad nie są ścisłe względem działania przetwarzania wsadowego i są przestrzegane na podstawie najlepszego wysiłku. Przy niskich współczynnikach zdarzeń często zaobserwujesz rozmiar wsadu mniejszy niż żądane maksymalne zdarzenia na partię.

* Wartość domyślna to OFF

  Domyślnie Event Grid dodaje tylko jedno zdarzenie do każdego żądania dostarczania. Aby włączyć tworzenie partii, należy ustawić jedno z ustawień wymienionych wcześniej w artykule w pliku JSON subskrypcji zdarzeń.

* Wartości domyślne

  Podczas tworzenia subskrypcji zdarzeń nie jest konieczne Określanie ustawień (maksymalna liczba zdarzeń na partię i przybliżony rozmiar partii w kilobajtach). Jeśli ustawiono tylko jedno ustawienie, Event Grid używa wartości domyślnych (konfigurowalne). W poniższych sekcjach znajdują się wartości domyślne i sposoby ich przesłonięcia.

## <a name="turn-on-output-batching"></a>Włącz przetwarzanie wsadowe danych wyjściowych

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Konfigurowanie maksymalnej dozwolonej wartości

Poniższe ustawienia czasu wdrożenia kontrolują maksymalną dozwoloną wartość podczas tworzenia subskrypcji zdarzeń.

| Nazwa właściwości | Opis |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maksymalna dozwolona wartość dla `PreferredBatchSizeInKilobytes` pokrętła. Wartość domyślna `1033` .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maksymalna dozwolona wartość dla `MaxEventsPerBatch` pokrętła. Wartość domyślna `50` .

## <a name="configuring-runtime-default-values"></a>Konfigurowanie wartości domyślnych środowiska uruchomieniowego

Następujące ustawienia czasu wdrożenia kontrolują wartość domyślną środowiska uruchomieniowego dla każdego pokrętła, gdy nie jest ono określone w subskrypcji zdarzeń. Aby przeprowadzić ponownie iterację, należy ustawić co najmniej jedno pokrętło w subskrypcji zdarzeń, aby włączyć przetwarzanie wsadowe.

| Nazwa właściwości | Opis |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maksymalny rozmiar żądania dostarczania, gdy `MaxEventsPerBatch` jest określony tylko. Wartość domyślna `1_058_576` .
| `broker__defaultMaxEventsPerBatch` | Maksymalna liczba zdarzeń do dodania do partii, gdy tylko `MaxBatchSizeInBytes` zostanie określony. Wartość domyślna `10` .
