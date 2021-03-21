---
title: Rozwiązywanie problemów z Azure Cosmos DB HTTP 408 lub Rozwiązywanie problemów z limitem czasu żądania przy użyciu zestawu .NET SDK
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki limitu czasu żądania zestawu SDK platformy .NET.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 03/05/2021
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8d35f7c666562022f503b2777f30f84193d0231
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440007"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnozowanie i rozwiązywanie problemów z wyjątkami limitu czasu żądania Azure Cosmos DB .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Błąd HTTP 408 występuje, jeśli zestaw SDK nie mógł wykonać żądania przed upływem limitu czasu.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Dostosuj limit czasu w zestawie Azure Cosmos DB .NET SDK

Zestaw SDK ma dwa różne alternatywy do kontrolowania limitów czasu, z których każdy ma inny zakres.

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout`Konfiguracja (lub `ConnectionPolicy.RequestTimeout` dla zestawu SDK V2) pozwala ustawić limit czasu, który ma wpływ na poszczególne żądania sieciowe. Operacja uruchomiona przez użytkownika może obejmować wiele żądań sieciowych (na przykład może to być ograniczenie przepustowości). Ta konfiguracja zostanie zastosowana do każdego żądania sieci przy ponownym ponowieniu próby. Ten limit czasu nie jest limitem czasu żądania operacji end-to-end.

### <a name="cancellationtoken"></a>CancellationToken

Wszystkie operacje asynchroniczne w zestawie SDK mają opcjonalny parametr CancellationToken. Ten parametr [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) jest używany w całej operacji we wszystkich żądaniach sieci. W przypadku między żądaniami sieci token anulowania może zostać sprawdzony i operacja została anulowana w przypadku wygaśnięcia powiązanego tokenu. Token anulowania powinien służyć do definiowania przybliżonego oczekiwanego limitu czasu dla zakresu operacji.

> [!NOTE]
> `CancellationToken`Parametr jest mechanizmem, w którym biblioteka będzie sprawdzać anulowanie, gdy [nie spowoduje nieprawidłowego stanu](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Operacja może się nie powieść dokładnie wtedy, gdy czas zdefiniowany w anulowania jest ustawiony. Zamiast tego, po upływie tego czasu, zostanie anulowany, gdy jest to bezpieczne.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania dla wyjątków limitu czasu żądania.

### <a name="high-cpu-utilization"></a>Wysoki poziom wykorzystania procesora
Wysokie wykorzystanie procesora CPU jest najpopularniejszym przypadkiem. W celu uzyskania optymalnego opóźnienia użycie procesora CPU powinno wynosić około 40 procent. Użyj 10 sekund jako interwału do monitorowania maksymalnego (nieśredniego) użycia procesora. Większe obciążenia procesora są bardziej typowe w przypadku zapytań między partycjami, w których może istnieć wiele połączeń dla jednego zapytania.

Jeśli błąd zawiera `TransportException` informacje, może on również zawierać `CPU History` :

```
CPU history: 
(2020-08-28T00:40:09.1769900Z 0.114), 
(2020-08-28T00:40:19.1763818Z 1.732), 
(2020-08-28T00:40:29.1759235Z 0.000), 
(2020-08-28T00:40:39.1763208Z 0.063), 
(2020-08-28T00:40:49.1767057Z 0.648), 
(2020-08-28T00:40:59.1689401Z 0.137), 
CPU count: 8)
```

* Jeśli pomiary procesora CPU przekraczają 70%, limit czasu może być spowodowany wyczerpaniem procesora. W takim przypadku rozwiązaniem jest zbadanie źródła dużego użycia procesora CPU i zmniejszenie go lub przeskalowanie maszyny do większego rozmiaru zasobów.
* Jeśli pomiary procesora CPU nie są wykonywane co 10 sekund (np. przerwy lub czasy pomiaru wskazują na większe odstępy między pomiarami), przyczyną jest przetrzymanie wątku. W takim przypadku rozwiązaniem jest zbadanie źródła przetrzymania wątku (potencjalnie zablokowane wątki) lub przeskalowanie maszyny do większego rozmiaru zasobów.

#### <a name="solution"></a>Rozwiązanie:
Aplikacja kliencka korzystająca z zestawu SDK powinna być skalowana w górę lub w dół.

### <a name="socket-or-port-availability-might-be-low"></a>Dostępność gniazda lub portu może być niska
W przypadku uruchamiania na platformie Azure klienci korzystający z zestawu SDK platformy .NET mogą osiągać wyczerpanie portów z użyciem usługi Azure translatora adresów sieciowych.

#### <a name="solution-1"></a>Rozwiązanie 1.
Jeśli korzystasz z maszyn wirtualnych platformy Azure, postępuj zgodnie z [przewodnikiem wyczerpania portów](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Rozwiązanie 2.
Jeśli korzystasz z programu na Azure App Service, postępuj zgodnie z [przewodnikiem rozwiązywania problemów z błędami połączenia](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) i [korzystaj z diagnostyki App Service](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Rozwiązanie 3:
W przypadku korzystania z programu na Azure Functions upewnij się, że korzystasz z [Azure Functions zalecenia](../azure-functions/manage-connections.md#static-clients) dotyczące utrzymania pojedynczych lub statycznych klientów dla wszystkich usług, w tym Azure Cosmos DB). Sprawdź [limity usługi](../azure-functions/functions-scale.md#service-limits) na podstawie typu i rozmiaru hostingu aplikacja funkcji.

#### <a name="solution-4"></a>Rozwiązanie 4:
W przypadku korzystania z serwera proxy HTTP upewnij się, że może on obsługiwać liczbę połączeń skonfigurowanych w zestawie SDK `ConnectionPolicy` . W przeciwnym razie nastąpi problem z połączeniem.

### <a name="create-multiple-client-instances"></a>Tworzenie wielu wystąpień klienta
Tworzenie wielu wystąpień klienta może prowadzić do problemów z rywalizacją i upłynięciem limitu czasu.

#### <a name="solution"></a>Rozwiązanie:
Postępuj zgodnie ze [wskazówkami dotyczącymi wydajności](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)i korzystaj z jednego wystąpienia CosmosClient w całym procesie.

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
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).