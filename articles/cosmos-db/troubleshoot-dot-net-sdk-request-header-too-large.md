---
title: Rozwiązywanie problemów z komunikatem "zbyt duży nagłówek żądania" lub 400 Nieprawidłowe żądanie w Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić zbyt duży wyjątek w nagłówku żądania.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: d5d66ca05390af5f6fef91ca959f1db3d547d3fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89014051"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Komunikat diagnozowanie i rozwiązywanie problemów Azure Cosmos DB "zbyt duży nagłówek żądania"
Komunikat "zbyt duży nagłówek żądania" jest generowany z kodem błędu HTTP 400. Ten błąd występuje, gdy rozmiar nagłówka żądania zwiększył się tak, że przekracza maksymalny dozwolony rozmiar. Zalecamy użycie najnowszej wersji zestawu SDK. Użyj co najmniej wersji 3. x lub 2. x, ponieważ te wersje dodają śledzenie rozmiaru nagłówka do komunikatu o wyjątku.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Komunikat "nagłówek żądania jest zbyt duży" występuje, gdy sesja lub token kontynuacji jest zbyt duży. W poniższych sekcjach opisano przyczynę problemu i jego rozwiązania w każdej kategorii.

### <a name="session-token-is-too-large"></a>Token sesji jest zbyt duży

#### <a name="cause"></a>Przyczyna:
Niewłaściwe żądanie 400 prawdopodobnie występuje, ponieważ token sesji jest zbyt duży. Jeśli następujące instrukcje są prawdziwe, token sesji jest zbyt duży:

* Ten błąd występuje w przypadku operacji na punktach, takich jak tworzenie, odczytywanie i aktualizowanie, gdzie nie ma tokenu kontynuacji.
* Wyjątek uruchomiony bez wprowadzania jakichkolwiek zmian w aplikacji. Token sesji rośnie wraz ze wzrostem liczby partycji w kontenerze. Liczba partycji zwiększa się wraz ze wzrostem ilości danych lub zwiększeniem przepływności.

#### <a name="temporary-mitigation"></a>Tymczasowe środki zaradcze: 
Aby zresetować wszystkie tokeny sesji, uruchom ponownie aplikację kliencką. Ostatecznie token sesji zostanie powiększony do poprzedniego rozmiaru, który spowodował problem. Aby uniknąć tego problemu, użyj rozwiązania w następnej sekcji.

#### <a name="solution"></a>Rozwiązanie:
1. Postępuj zgodnie ze wskazówkami zawartymi w artykułach z porad dotyczących wydajności [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) lub [.NET V2](performance-tips.md) . Przekonwertuj aplikację tak, aby korzystała z trybu połączenia bezpośredniego z Transmission Control Protocol (TCP). Tryb połączenia bezpośredniego z protokołem TCP nie ma ograniczenia rozmiaru nagłówka, takiego jak protokół HTTP, więc pozwala uniknąć tego problemu. Upewnij się, że używasz najnowszej wersji zestawu SDK, która ma poprawkę dla operacji zapytania, gdy usługa Interop nie jest dostępna.
1. Jeśli tryb połączenia bezpośredniego z protokołem TCP nie jest opcją dla obciążenia, można go wyeliminować przez zmianę [poziomu spójności klienta](how-to-manage-consistency.md). Token sesji jest używany tylko w przypadku spójności sesji, czyli domyślnego poziomu spójności dla Azure Cosmos DB. Inne poziomy spójności nie używają tokenu sesji.

### <a name="continuation-token-is-too-large"></a>Token kontynuacji jest zbyt duży

#### <a name="cause"></a>Przyczyna:
Nieprawidłowe żądanie 400 dotyczy operacji zapytania, w których jest używany token kontynuacji, jeśli token kontynuacji jest zbyt duży lub różne zapytania mają różne rozmiary tokenu kontynuacji.
    
#### <a name="solution"></a>Rozwiązanie:
1. Postępuj zgodnie ze wskazówkami zawartymi w artykułach z porad dotyczących wydajności [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) lub [.NET V2](performance-tips.md) . Przekonwertuj aplikację tak, aby korzystała z trybu połączenia bezpośredniego z protokołem TCP. Tryb połączenia bezpośredniego z protokołem TCP nie ma ograniczenia rozmiaru nagłówka, takiego jak protokół HTTP, więc pozwala uniknąć tego problemu. 
1. Jeśli tryb połączenia bezpośredniego z protokołem TCP nie jest opcją dla obciążenia, ustaw `ResponseContinuationTokenLimitInKb` opcję. Tę opcję można znaleźć w `FeedOptions` wersji 2 lub `QueryRequestOptions` 3.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).
