---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: b2e753a3f9741856dd8b81755912ad7bd78b5557
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711421"
---
Usługa Azure Storage udostępnia następujące metryki transakcji w Azure Monitor.

| Metryka | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: responsetype, geotype, ApiName i Authentication ([Definicja](#metrics-dimensions))<br/> Przykład wartości: 1024 |
| Ruch przychodzący | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Dostępność | Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości wszystkich żądań do rozliczenia przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: procent <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 99,99 |