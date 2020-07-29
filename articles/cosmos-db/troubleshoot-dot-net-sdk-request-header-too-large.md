---
title: Rozwiązywanie problemów z nagłówkiem żądania za duże lub 400 Nieprawidłowe żądanie w Azure Cosmos DB
description: Jak zdiagnozować i naprawić zbyt duży wyjątek w nagłówku żądania
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294673"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnozowanie i rozwiązywanie problemów z nagłówkiem żądania Azure Cosmos DB zbyt duży komunikat
W nagłówku żądania jest zbyt duży komunikat z kodem błędu HTTP 400. Ten błąd występuje, gdy rozmiar nagłówka żądania jest zbyt duży i przekracza maksymalny dozwolony rozmiar. Zalecamy użycie najnowszej wersji zestawu SDK. Upewnij się, że używasz co najmniej wersji 3. x lub 2. x, ponieważ te wersje dodają śledzenie rozmiaru nagłówka do komunikatu o wyjątku.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Zbyt duży komunikat nagłówka żądania, jeśli sesja lub token kontynuacji jest zbyt duży. W poniższych sekcjach opisano przyczynę i rozwiązanie problemu w każdej kategorii.

### <a name="1-session-token-too-large"></a>1. token sesji jest zbyt duży

#### <a name="cause"></a>Przyczyna:
400 Nieprawidłowe żądanie jest najprawdopodobniej spowodowane przez token sesji, który jest duży. Jeśli następujące instrukcje są prawdziwe, potwierdza, że token sesji jest zbyt duży.

* Wystąpił błąd podczas operacji wskazywania, takich jak Create, Read, Update i itp. w przypadku braku tokenu kontynuacji.
* Wyjątek uruchomiony bez wprowadzania jakichkolwiek zmian w aplikacji. Token sesji rośnie wraz ze wzrostem liczby partycji w kontenerze. Liczba partycji zwiększa się w miarę wzrostu ilości danych lub wzrostu przepływności.

#### <a name="temporary-mitigation"></a>Tymczasowe środki zaradcze: 
Aby zresetować wszystkie tokeny sesji, uruchom ponownie aplikację kliencką. Token sesji zostanie ostatecznie powiększony do poprzedniego rozmiaru, który spowodował problem. Użyj rozwiązania w następnej sekcji, aby uniknąć całkowitego tego problemu.

#### <a name="solution"></a>Rozwiązanie:
1. Postępuj zgodnie ze wskazówkami zawartymi w artykule wskazówki dotyczące wydajności [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) lub [.NET V2](performance-tips.md) i Konwertuj aplikację tak, aby korzystała z trybu połączenia bezpośredniego z protokołem TCP. Tryb bezpośredni z protokołem TCP nie ma ograniczenia rozmiaru nagłówka, takiego jak protokół HTTP, więc pozwala uniknąć tego problemu. Upewnij się, że korzystasz z najnowszej wersji zestawu SDK, która ma poprawkę dla operacji zapytania, gdy usługa międzyoperacyjna usługi jest niedostępna.
2. Jeśli tryb połączenia bezpośredniego z protokołem TCP nie jest opcją dla obciążenia, można go wyeliminować przez zmianę [poziomu spójności klienta](how-to-manage-consistency.md). Token sesji jest używany tylko w przypadku spójności sesji, czyli domyślnego poziomu spójności dla Azure Cosmos DB. Inne poziomy spójności nie używają tokenu sesji.

### <a name="2-continuation-token-too-large"></a>2. token kontynuacji jest zbyt duży

#### <a name="cause"></a>Przyczyna:
Nieprawidłowe żądanie 400 jest wykonywane w przypadku operacji zapytania, w których jest używany token kontynuacji. Jeśli token kontynuacji jest zbyt duży lub jeśli różne zapytania mają różne rozmiary tokenu kontynuacji.
    
#### <a name="solution"></a>Rozwiązanie:
1. Postępuj zgodnie ze wskazówkami zawartymi w artykule wskazówki dotyczące wydajności [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) lub [.NET V2](performance-tips.md) i Konwertuj aplikację tak, aby korzystała z trybu połączenia bezpośredniego z protokołem TCP. Tryb bezpośredni z protokołem TCP nie ma ograniczenia rozmiaru nagłówka, takiego jak protokół HTTP, więc pozwala uniknąć tego problemu. 
3. Jeśli tryb połączenia bezpośredniego z protokołem TCP nie jest opcją dla obciążenia, spróbuj ustawić `ResponseContinuationTokenLimitInKb` opcję. Tę opcję można znaleźć w `FeedOptions` wersji 2 lub `QueryRequestOptions` 3.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)
