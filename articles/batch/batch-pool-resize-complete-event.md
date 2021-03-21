---
title: Zdarzenie ukończenia zmiany rozmiaru puli Azure Batch
description: Odwołanie do zdarzenia ukończenia zmiany rozmiaru puli usługi Batch. Zobacz przykład puli, która zwiększyła rozmiar i została ukończona pomyślnie.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 9d3342587b5f6e0e134f4295a8c79deeb23df94b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803650"
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

|Element|Typ|Uwagi|
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
