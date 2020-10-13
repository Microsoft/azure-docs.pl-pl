---
title: Zdarzenie ukończenia zmiany rozmiaru puli Azure Batch
description: Odwołanie do zdarzenia ukończenia zmiany rozmiaru puli usługi Batch. Zobacz przykład puli, która zwiększyła rozmiar i została ukończona pomyślnie.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 94301f29fb6e7968dbe0389754fcf2a3b105d7ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83723820"
---
# <a name="pool-resize-complete-event"></a>Zdarzenie zakończenia zmiany rozmiaru puli

 To zdarzenie jest emitowane, gdy zmiana rozmiaru puli zakończyła się lub zakończyła się niepowodzeniem.

 Poniższy przykład pokazuje treść puli, która zmienia rozmiar dla puli, która zwiększyła rozmiar i została ukończona pomyślnie.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Element|Type|Uwagi|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator puli.|
|`nodeDeallocationOption`|Ciąg|Określa, kiedy węzły mogą zostać usunięte z puli, jeśli rozmiar puli zmniejsza się.<br /><br /> Możliwe wartości:<br /><br /> **Kolejka** — kończy wykonywanie zadań i umieszcza je w kolejce. Zadania zostaną uruchomione ponownie, gdy zadanie zostanie włączone. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **Przerwij** — kończy wykonywanie zadań. Zadania nie zostaną uruchomione ponownie. Usuń węzły zaraz po zakończeniu zadań.<br /><br /> **taskcompletion** — umożliwia wykonywanie obecnie uruchomionych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po zakończeniu wszystkich zadań.<br /><br /> **Retaineddata** — Zezwalaj na wykonywanie aktualnie uruchomionych zadań, a następnie zaczekaj na wygaśnięcie wszystkich okresów przechowywania danych zadań. Zaplanuj brak nowych zadań podczas oczekiwania. Usuń węzły po wygaśnięciu wszystkich okresów przechowywania zadań.<br /><br /> Wartość domyślna to requeue.<br /><br /> W przypadku zwiększenia rozmiaru puli wartość jest ustawiana jako **nieprawidłowa**.|
|`currentDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych, które są obecnie przypisane do puli.|
|`targetDedicatedNodes`|Int32|Liczba dedykowanych węzłów obliczeniowych, które są żądane dla puli.|
|`currentLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie, które są obecnie przypisane do puli.|
|`targetLowPriorityNodes`|Int32|Liczba węzłów obliczeniowych o niskim priorytecie, które są żądane dla puli.|
|`enableAutoScale`|Wartość logiczna|Określa, czy rozmiar puli automatycznie dostosowuje się w miarę upływu czasu.|
|`isAutoPool`|Wartość logiczna|Określa, czy pula została utworzona za pomocą mechanizmu autopuli zadań.|
|`startTime`|DateTime|Godzina, o której zmiana rozmiaru puli została rozpoczęta.|
|`endTime`|DateTime|Godzina, o której zmiana rozmiaru puli została zakończona.|
|`resultCode`|Ciąg|Wynik zmiany rozmiaru.|
|`resultMessage`|Ciąg| Szczegółowy komunikat dotyczący wyniku.<br /><br /> Jeśli zmiana rozmiaru została zakończona pomyślnie, stwierdza, że operacja powiodła się.|
