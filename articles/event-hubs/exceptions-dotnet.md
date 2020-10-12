---
title: Azure Event Hubs — wyjątki dla platformy .NET
description: Ten artykuł zawiera listę wyjątków usługi Azure Event Hubs .NET do obsługi komunikatów i sugerowanych akcji.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: shvija
ms.openlocfilehash: 6a1d7c969d31033ae9d00d212cc8f1a45abbeda1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347394"
---
# <a name="eventhubsexception---net"></a>EventHubsException — .NET
**EventHubsException** jest wyzwalany, gdy operacja określona dla Event Hubs spowodowała problem, w tym zarówno błędy w ramach usługi, jak i specyficzne dla klienta. 

## <a name="exception-information"></a>Informacje o wyjątku
Wyjątek zawiera następujące informacje kontekstowe, aby pomóc w zrozumieniu kontekstu błędu i jego względnej ważności. 

- **Isprzejściowy**: określa, czy wyjątek jest uznawany za odwracalny, czy nie. W przypadku, gdy została uznana za przejściowo, odpowiednie zasady ponawiania zostały już zastosowane i ponawianie próby nie powiodło się.
- **Przyczyna**: zawiera zestaw dobrze znanych przyczyn niepowodzenia, które ułatwiają kategoryzowanie i wyjaśnienie głównej przyczyny problemu. Te powody mają na celu umożliwienie stosowania filtrowania wyjątków i innych logiki podczas sprawdzania tekstu komunikatu o wyjątku nie byłoby idealnym rozwiązaniem. Niektóre przyczyny niepowodzenia:
    - **Klient zamknięty**: występuje, gdy klient centrum zdarzeń został już zamknięty lub usunięty. Zalecamy sprawdzenie kodu aplikacji, aby upewnić się, że obiekty z biblioteki klienta Event Hubs są tworzone i zamykane w zamierzonym zakresie.
    - **Limit czasu usługi**: wskazuje, że usługa Event Hubs nie odpowiadała na operację w oczekiwanym czasie. Przyczyną tego problemu może być przejściowy problem z siecią lub usługa. Usługa Event Hubs może lub nie pomyślnie zakończyć żądania; stan nie jest znany. Zalecane jest, aby spróbować sprawdzić bieżący stan i ponowić próbę w razie potrzeby.
    - **Przekroczono limit przydziału**: wskazuje, że istnieje zbyt wiele aktywnych operacji odczytu dla jednej grupy odbiorców. Ten limit zależy od warstwy przestrzeni nazw Event Hubs i może być wymagana zmiana warstwy na wyższą. Alternatywą jest utworzenie dodatkowych grup odbiorców i upewnienie się, że liczba odczytów klienta konsumenta dla każdej grupy przekracza limit. Aby uzyskać więcej informacji, zobacz [przydziały i limity usługi Azure Event Hubs](event-hubs-quotas.md).
    - **Przekroczono rozmiar komunikatu**: dane zdarzenia mają maksymalny rozmiar dozwolony zarówno dla pojedynczego zdarzenia, jak i partii zdarzeń. Obejmuje ona dane zdarzenia oraz wszelkie skojarzone metadane i obciążenie systemu. Aby rozwiązać ten problem, zmniejsz liczbę zdarzeń wysyłanych w partii lub Zmniejsz rozmiar danych zawartych w komunikacie. Ponieważ limity rozmiaru mogą ulec zmianie, zapoznaj się z artykułem na temat [przydziałów i limitów platformy Azure Event Hubs](event-hubs-quotas.md) dla określonych.
    - **Odłączony odbiorca**: klient klienta został rozłączony przez usługę centrum zdarzeń z wystąpienia centrum zdarzeń. Zwykle występuje, gdy konsument o wyższym poziomie właściciela potwierdza własność przez parowanie partycji i grupy odbiorców.
    - **Nie znaleziono zasobu**: usługa Event Hubs nie może znaleźć zasobu, takiego jak centrum zdarzeń, Grupa konsumentów lub partycja. Mógł zostać usunięty lub wystąpił problem dotyczący samej usługi Event Hubs.

## <a name="handling-exceptions"></a>Obsługa wyjątków
Można reagować na konkretną przyczynę niepowodzenia dla **EventHubException**  na kilka sposobów. Jednym ze sposobów jest stosowanie klauzuli filtru wyjątków jako części bloku catch.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Następne kroki
Istnieją inne wyjątki, które zostały udokumentowane w [starszym artykule](event-hubs-messaging-exceptions.md). Niektóre z nich mają zastosowanie tylko do starszej biblioteki klienta Event Hubs .NET.