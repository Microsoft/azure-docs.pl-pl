---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96536922"
---
Usługa Azure Storage udostępnia następujące metryki transakcji w Azure Monitor.

| Metric | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: responsetype, geotype, ApiName i Authentication ([Definicja](#metrics-dimensions))<br/> Przykład wartości: 1024 |
| Ruch przychodzący | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. Różnica między wartościami SuccessE2ELatency i SuccessServerLatency to opóźnienie prawdopodobnie spowodowane przez sieć i klienta.<br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Dostępność | Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości wszystkich żądań do rozliczenia przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: procent <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 99,99 |
