---
title: Rozwiązywanie problemów z Azure Cosmos DB HTTP 408 lub Rozwiązywanie problemów z limitem czasu żądania przy użyciu zestawu .NET SDK
description: Dowiedz się, jak zdiagnozować i naprawić wyjątki limitu czasu żądania zestawu SDK platformy .NET.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 0c760a3a2f6300108c1739f18ef9fa97a40dd833
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021939"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnozowanie i rozwiązywanie problemów z wyjątkami limitu czasu żądania Azure Cosmos DB .NET SDK
Błąd HTTP 408 występuje, jeśli zestaw SDK nie mógł wykonać żądania przed upływem limitu czasu.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Dostosuj limit czasu w zestawie Azure Cosmos DB .NET SDK

Zestaw SDK ma dwa różne alternatywy do kontrolowania limitów czasu, z których każdy ma inny zakres.

### <a name="requesttimeout"></a>RequestTimeout

`CosmosClientOptions.RequestTimeout`Konfiguracja (lub `ConnectionPolicy.RequestTimeout` dla zestawu SDK V2) pozwala ustawić limit czasu, który ma wpływ na poszczególne żądania sieciowe. Operacja uruchomiona przez użytkownika może obejmować wiele żądań sieciowych (na przykład może to być ograniczenie przepustowości). Ta konfiguracja zostanie zastosowana do każdego żądania sieci przy ponownym ponowieniu próby. Ten limit czasu nie jest limitem czasu żądania operacji end-to-end.

### <a name="cancellationtoken"></a>CancellationToken

Wszystkie operacje asynchroniczne w zestawie SDK mają opcjonalny parametr CancellationToken. Ten parametr [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) jest używany w całej operacji we wszystkich żądaniach sieci. W przypadku między żądaniami sieci token anulowania może zostać sprawdzony i operacja została anulowana w przypadku wygaśnięcia powiązanego tokenu. Token anulowania powinien służyć do definiowania przybliżonego oczekiwanego limitu czasu dla zakresu operacji.

> [!NOTE]
> `CancellationToken`Parametr jest mechanizmem, w którym biblioteka będzie sprawdzać anulowanie, gdy [nie spowoduje nieprawidłowego stanu](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Operacja może się nie powieść dokładnie wtedy, gdy czas zdefiniowany w anulowania jest ustawiony. Zamiast tego, po upływie tego czasu, zostanie anulowany, gdy jest to bezpieczne.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania dla wyjątków limitu czasu żądania.

### <a name="high-cpu-utilization"></a>Wysokie wykorzystanie procesora CPU
Wysokie wykorzystanie procesora CPU jest najpopularniejszym przypadkiem. W celu uzyskania optymalnego opóźnienia użycie procesora CPU powinno wynosić około 40 procent. Użyj 10 sekund jako interwału do monitorowania maksymalnego (nieśredniego) użycia procesora. Większe obciążenia procesora są bardziej typowe w przypadku zapytań między partycjami, w których może istnieć wiele połączeń dla jednego zapytania.

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
