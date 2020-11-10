---
title: Rozwiązywanie problemów z Azure Cosmos DB HTTP 408 lub Rozwiązywanie problemów z limitem czasu żądania przy użyciu zestawu SDK Java v4
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki limitu czasu żądania zestawu SDK języka Java za pomocą zestawu SDK języka Java v4.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a805300ac62d0627c9b06188c9764a6887947afe
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411290"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnozowanie i rozwiązywanie problemów Azure Cosmos DB wyjątków limitu czasu żądania zestawu SDK Java v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Błąd HTTP 408 występuje, jeśli zestaw SDK nie mógł wykonać żądania przed upływem limitu czasu.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania dla wyjątków limitu czasu żądania.

### <a name="existing-issues"></a>Istniejące problemy
Jeśli widzisz żądania, które mają być zablokowane przez dłuższy czas lub przekroczenia limitu czasu, Uaktualnij zestaw SDK dla języka Java v4 do najnowszej wersji. Uwaga: zdecydowanie zalecamy użycie wersji 4.7.0 i nowszych. Aby uzyskać więcej informacji, zapoznaj się z [informacjami o wersji zestawu SDK Java v4](sql-api-sdk-java-v4.md) .

### <a name="high-cpu-utilization"></a>Wysokie wykorzystanie procesora CPU
Wysokie wykorzystanie procesora CPU jest najpopularniejszym przypadkiem. W celu uzyskania optymalnego opóźnienia użycie procesora CPU powinno wynosić około 40 procent. Użyj 10 sekund jako interwału do monitorowania maksymalnego (nieśredniego) użycia procesora. Większe obciążenia procesora są bardziej typowe w przypadku zapytań między partycjami, w których może istnieć wiele połączeń dla jednego zapytania.

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę lub w dół.

### <a name="connection-throttling"></a>Ograniczanie połączeń
Możliwe jest ograniczenie połączenia z powodu limitu połączeń na komputerze hosta lub w wyczerpaniu portów usługi Azure translator adresów sieciowych.

### <a name="connection-limit-on-a-host-machine"></a>Limit połączeń na komputerze hosta
Niektóre systemy Linux, takie jak Red Hat, mają górny limit łącznej liczby otwartych plików. Gniazda w systemie Linux są zaimplementowane jako pliki, więc ta liczba ogranicza łączną liczbę połączeń. Uruchom następujące polecenie.

```bash
ulimit -a
```

#### <a name="solution"></a>Rozwiązanie:
Maksymalna dozwolona liczba otwartych plików, które są identyfikowane jako "nofile", musi wynosić co najmniej 10 000 lub więcej. Aby uzyskać więcej informacji, zapoznaj się ze [wskazówkami dotyczącymi wydajności](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4.

### <a name="socket-or-port-availability-might-be-low"></a>Dostępność gniazda lub portu może być niska
W przypadku uruchamiania na platformie Azure klienci korzystający z zestawu SDK języka Java mogą trafiać z wyczerpania portów przy użyciu usługi Azure translatora adresów sieciowych.

#### <a name="solution-1"></a>Rozwiązanie 1.
Jeśli korzystasz z maszyn wirtualnych platformy Azure, postępuj zgodnie z [przewodnikiem wyczerpania portów](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Rozwiązanie 2.
Jeśli korzystasz z programu na Azure App Service, postępuj zgodnie z [przewodnikiem rozwiązywania problemów z błędami połączenia](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) i [korzystaj z diagnostyki App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Rozwiązanie 3:
W przypadku korzystania z programu na Azure Functions upewnij się, że korzystasz z [Azure Functions zalecenia](../azure-functions/manage-connections.md#static-clients) dotyczące utrzymania pojedynczych lub statycznych klientów dla wszystkich usług, w tym Azure Cosmos DB). Sprawdź [limity usługi](../azure-functions/functions-scale.md#service-limits) na podstawie typu i rozmiaru hostingu aplikacja funkcji.

#### <a name="solution-4"></a>Rozwiązanie 4:
W przypadku korzystania z serwera proxy HTTP upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w zestawie SDK `GatewayConnectionConfig` . W przeciwnym razie nastąpi problem z połączeniem.

### <a name="create-multiple-client-instances"></a>Tworzenie wielu wystąpień klienta
Tworzenie wielu wystąpień klienta może prowadzić do problemów z rywalizacją i upłynięciem limitu czasu.

#### <a name="solution-1"></a>Rozwiązanie 1.
Postępuj zgodnie ze [wskazówkami dotyczącymi wydajności](performance-tips-java-sdk-v4-sql.md#sdk-usage)i korzystaj z jednego wystąpienia CosmosClient w całej aplikacji.

#### <a name="solution-2"></a>Rozwiązanie 2.
Jeśli pojedyncze CosmosClient nie są dostępne w aplikacji, zalecamy używanie udostępniania połączenia między wieloma klientami Cosmos za pośrednictwem tego interfejsu API `connectionSharingAcrossClientsEnabled(true)` w CosmosClient. W przypadku wielu wystąpień klienta Cosmos w tym samym JVM, które współdziałają z wieloma kontami Cosmos, włączenie tej funkcji umożliwia udostępnianie połączenia w trybie bezpośrednim, jeśli jest to możliwe między wystąpieniami programu Cosmos Client. Należy pamiętać, że podczas ustawiania tej opcji konfiguracja połączenia (np. Konfiguracja limitu czasu gniazda, konfiguracja limitu czasu bezczynności) pierwszego wystąpienia klienta zostanie użyta dla wszystkich innych wystąpień klienta.

### <a name="hot-partition-key"></a>Klucz partycji aktywnej
Azure Cosmos DB dystrybuuje ogólnie zainicjowaną przepływność równomiernie między partycjami fizycznymi. Gdy istnieje partycja gorąca, co najmniej jeden klucz partycji logicznej na partycji fizycznej zużywa wszystkie jednostki żądań partycji fizycznej na sekundę (RU/s). W tym samym czasie jednostki RU/s na innych partycjach fizycznych nie są używane. Z punktu widzenia całkowita ilość zużywanych przez RU/s będzie mniejsza niż ogólnie obsługiwana wartość RU/s w bazie danych lub kontenerze, ale nadal będzie widoczne ograniczenie przepustowości (429s) na żądania dotyczące klucza logicznej partycji. Użyj [znormalizowanej metryki zużycia ru](monitor-normalized-request-units.md) , aby sprawdzić, czy obciążenie napotyka partycję gorącą. 

#### <a name="solution"></a>Rozwiązanie:
Wybierz dobry klucz partycji, który równomiernie dystrybuuje wolumin żądania i magazyn. Dowiedz się, jak [zmienić klucz partycji](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Wysoki stopień współbieżności
Aplikacja wykonuje wysoki poziom współbieżności, co może prowadzić do rywalizacji o kanał.

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę lub w dół.

### <a name="large-requests-or-responses"></a>Duże żądania lub odpowiedzi
Duże żądania lub odpowiedzi mogą prowadzić do blokowania w kanale i zaostrzania rywalizacji nawet przy stosunkowo niskim stopniu współbieżności.

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę lub w dół.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>Częstotliwość niepowodzeń jest objęta umową SLA Azure Cosmos DB
Aplikacja powinna być w stanie obsługiwać błędy przejściowe i ponawiać próbę w razie potrzeby. Wyjątki 408 nie są ponawiane, ponieważ przy tworzeniu ścieżek nie jest możliwe, że usługa utworzyła element. Ponowne wysłanie tego samego elementu dla tworzenia spowoduje wystąpienie wyjątku konfliktu. Logika biznesowa aplikacji użytkownika może być logiką niestandardową, aby obsłużyć konflikty, co spowodowałoby przerwanie od niejednoznaczności istniejącego elementu, a następnie konflikt operacji tworzenia.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>Współczynnik błędów narusza Azure Cosmos DB umowy SLA
Skontaktuj się z [pomocą techniczną platformy Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-java-sdk-v4-sql.md) problemów w przypadku używania Azure Cosmos DB Java v4 SDK.
* Poznaj wskazówki dotyczące wydajności dla [środowiska Java v4](performance-tips-java-sdk-v4-sql.md).
