---
title: Rozwiązywanie problemów dotyczących Azure Cosmos DB HTTP 408 lub problemów z limitem czasu żądania przy użyciu zestawu .NET SDK
description: Jak zdiagnozować i naprawić wyjątek limitu czasu żądania zestawu SDK platformy .NET
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 29b0c6237ae04ea5da9ec496498fc7c20890b173
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294633"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB limit czasu żądania zestawu SDK platformy .NET
Błąd HTTP 408 występuje, jeśli zestaw SDK nie mógł wykonać żądania przed upływem limitu czasu.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania dla wyjątków limitu czasu żądania.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. wysokie wykorzystanie procesora CPU (najbardziej typowe)
W celu uzyskania optymalnego opóźnienia zaleca się użycie procesora CPU w około 40%. Zalecane jest użycie 10 sekund jako interwału monitorowania maksymalnego (nieśredniego) użycia procesora. Większe obciążenia procesora są bardziej typowe w przypadku zapytań między partycjami, w których może istnieć wiele połączeń dla jednego zapytania.

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę/w poziomie.

### <a name="2-socket--port-availability-might-be-low"></a>2. dostępność gniazda/portu może być niska
W przypadku uruchamiania na platformie Azure klienci korzystający z zestawu SDK platformy .NET mogą osiągać wyczerpanie portów z użyciem usługi Azure translatora adresów sieciowych.

#### <a name="solution-1"></a>Rozwiązanie 1.
Postępuj zgodnie z [przewodnikiem wyczerpania portów](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Rozwiązanie 2.
W przypadku korzystania z serwera proxy HTTP upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w zestawie SDK `ConnectionPolicy` .
W przeciwnym razie nastąpiły problemy z połączeniem.

### <a name="3-creating-multiple-client-instances"></a>3. Tworzenie wielu wystąpień klienta
Tworzenie wielu wystąpień klienta może prowadzić do problemów z rywalizacją i upłynięciem limitu czasu.

#### <a name="solution"></a>Rozwiązanie:
Postępuj zgodnie ze [wskazówkami dotyczącymi wydajności](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)i korzystaj z jednego wystąpienia CosmosClient w całym procesie.

### <a name="4-hot-partition-key"></a>4. klucz partycji aktywnej
Azure Cosmos DB dystrybuuje ogólnie zainicjowaną przepływność równomiernie między partycjami fizycznymi. Gdy istnieje partycja gorąca, co najmniej jeden klucz partycji logicznej na partycji fizycznej zużywa wszystkie wystąpienia RU/s partycji fizycznej, podczas gdy nie są one używane na innych partycjach fizycznych. Z punktu widzenia całkowita ilość zużywanych przez RU/s będzie mniejsza niż ogólnie obsługiwana wartość RU/s w bazie danych lub kontenerze, ale nadal będzie widoczne ograniczenie przepustowości (429s) na żądania dotyczące klucza logicznej partycji. Użyj [znormalizowanej metryki zużycia ru](monitor-normalized-request-units.md) , aby sprawdzić, czy obciążenie napotyka partycję gorącą. 

#### <a name="solution"></a>Rozwiązanie:
Wybierz dobry klucz partycji, który równomiernie dystrybuuje wolumin żądania i magazyn. Dowiedz się, jak [zmienić klucz partycji](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. wysoki stopień współbieżności
Aplikacja wykonuje wysoki poziom współbieżności, co może prowadzić do rywalizacji o kanał

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę/w poziomie.

### <a name="6-large-requests-andor-responses"></a>6. duże żądania i/lub odpowiedzi
Duże żądania lub odpowiedzi mogą prowadzić do blokowania w kanale i zaostrzania rywalizacji nawet przy stosunkowo niskim stopniu współbieżności.

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę/w poziomie.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. częstotliwość niepowodzeń jest objęta umową SLA Cosmos DB
Aplikacja powinna być w stanie obsługiwać błędy przejściowe i ponawiać próbę w razie potrzeby. 408 wyjątki nie są ponawiane, ponieważ przy tworzeniu ścieżek nie jest możliwe, że usługa utworzyła element lub jeśli go nie ma. Ponowne wysłanie tego samego elementu dla tworzenia spowoduje wystąpienie wyjątku konfliktu. Logika biznesowa aplikacji użytkownika może być logiką niestandardową, aby obsłużyć konflikty, co spowodowałoby przerwanie od niejednoznaczności istniejącego elementu a konflikt operacji tworzenia.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. częstotliwość niepowodzeń narusza Cosmos DB umowy SLA
Skontaktuj się z pomocą techniczną platformy Azure.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)