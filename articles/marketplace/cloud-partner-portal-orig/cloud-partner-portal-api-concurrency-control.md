---
title: Kontrola współbieżności | Portal Azure Marketplace
description: Strategie kontroli współbieżności dla interfejsów API publikowania portal Cloud Partner.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256403"
---
# <a name="concurrency-control"></a>Kontrola współbieżności

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Każde wywołanie interfejsów API publikowania portal Cloud Partner musi jawnie określić strategię kontroli współbieżności, która ma być używana. Niedostarczenie nagłówka **if-Match** spowoduje wystąpienie błędu HTTP 400. Oferujemy dwie strategie kontroli współbieżności.

-   **Optymistyczny** — klient wykonujący aktualizację weryfikuje, czy dane zostały zmienione od czasu ostatniego odczytu danych.
-   **Ostatni serwer WINS** — klient bezpośrednio aktualizuje dane, bez względu na to, czy inna aplikacja zmodyfikował ją od czasu ostatniego odczytu.

<a name="optimistic-concurrency-workflow"></a>Optymistyczny przepływ pracy współbieżności
-------------------------------

Zalecamy korzystanie z strategii optymistycznej współbieżności z następującym przepływem pracy w celu zagwarantowania, że w Twoich zasobach nie wprowadzono nieoczekiwanych zmian.

1.  Pobieranie jednostki przy użyciu interfejsów API. Odpowiedź zawiera wartość ETag, która identyfikuje aktualnie przechowywaną wersję jednostki (w czasie odpowiedzi).
2.  W czasie aktualizacji należy uwzględnić tę samą wartość ETag w nagłówku żądania obowiązkowego **if-Match** .
3.  Interfejs API porównuje wartość ETag odebraną w żądaniu z bieżącą wartością ETag jednostki w transakcji niepodzielnej.
    *   Jeśli wartości ETag są różne, interfejs API zwraca odpowiedź `412 Precondition Failed` http. Ten błąd wskazuje, że inny proces zaktualizował jednostkę od momentu ostatniego pobrania przez klienta lub że wartość ETag określona w żądaniu jest niepoprawna.
    *  Jeśli wartości ETag są takie same lub nagłówek **if-Match** zawiera symbol wieloznaczny gwiazdki (`*`), interfejs API wykonuje żądaną operację. Operacja interfejsu API aktualizuje także przechowywaną wartość ETag obiektu.


> [!NOTE]
> Określenie symbolu wieloznacznego (*) w nagłówku **if-Match** spowoduje użycie interfejsu API w ramach strategii współbieżności z ostatnich jednego serwera WINS. W takim przypadku porównanie ETag nie występuje, a zasób jest aktualizowany bez żadnych testów. 
