---
title: Kontrola współbieżności — Azure Marketplace
description: Strategie kontroli współbieżności dla interfejsów API publikowania portal Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e468898daffe8fc42250575d3efa42f99279c410
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88031652"
---
# <a name="concurrency-control"></a>Kontrola współbieżności

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Każde wywołanie interfejsów API publikowania portal Cloud Partner musi jawnie określić strategię kontroli współbieżności, która ma być używana. Niedostarczenie nagłówka **if-Match** spowoduje wystąpienie błędu HTTP 400. Oferujemy dwie strategie kontroli współbieżności.

-   **Optymistyczny** — klient wykonujący aktualizację weryfikuje, czy dane zostały zmienione od czasu ostatniego odczytu danych.
-   **Ostatni serwer WINS** — klient bezpośrednio aktualizuje dane, bez względu na to, czy inna aplikacja zmodyfikował ją od czasu ostatniego odczytu.

<a name="optimistic-concurrency-workflow"></a>Optymistyczny przepływ pracy współbieżności
-------------------------------

Zalecamy korzystanie z strategii optymistycznej współbieżności z następującym przepływem pracy w celu zagwarantowania, że w Twoich zasobach nie wprowadzono nieoczekiwanych zmian.

1.  Pobieranie jednostki przy użyciu interfejsów API. Odpowiedź zawiera wartość ETag, która identyfikuje aktualnie przechowywaną wersję jednostki (w czasie odpowiedzi).
2.  W czasie aktualizacji należy uwzględnić tę samą wartość ETag w nagłówku żądania obowiązkowego **if-Match** .
3.  Interfejs API porównuje wartość ETag odebraną w żądaniu z bieżącą wartością ETag jednostki w transakcji niepodzielnej.
    *   Jeśli wartości ETag są różne, interfejs API zwraca `412 Precondition Failed` odpowiedź HTTP. Ten błąd wskazuje, że inny proces zaktualizował jednostkę od momentu ostatniego pobrania przez klienta lub że wartość ETag określona w żądaniu jest niepoprawna.
    *  Jeśli wartości ETag są takie same lub nagłówek **if-Match** zawiera symbol wieloznaczny gwiazdki ( `*` ), interfejs API wykonuje żądaną operację. Operacja interfejsu API aktualizuje także przechowywaną wartość ETag obiektu.


> [!NOTE]
> Określenie symbolu wieloznacznego (*) w nagłówku **if-Match** spowoduje użycie interfejsu API w ramach strategii współbieżności z ostatnich jednego serwera WINS. W takim przypadku porównanie ETag nie występuje, a zasób jest aktualizowany bez żadnych testów. 
