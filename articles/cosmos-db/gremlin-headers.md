---
title: Azure Cosmos DB nagłówki odpowiedzi Gremlin
description: Dokumentacja referencyjna dla metadanych odpowiedzi serwera, która umożliwia dodatkowe Rozwiązywanie problemów
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600529"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB nagłówki odpowiedzi serwera Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

W tym artykule opisano nagłówki, które serwer Gremlin usługi Cosmos DB zwraca do obiektu wywołującego po wykonaniu żądania. Te nagłówki są przydatne podczas rozwiązywania problemów z wydajnością żądań, tworzenia aplikacji integrującej się natywnie z usługą Cosmos DB i upraszczania obsługi klientów.

Należy pamiętać, że w zależności od tych nagłówków ograniczenie przenośności aplikacji do innych implementacji Gremlin. W programie zwracasz ściślejszą integrację z usługą Cosmos DB Gremlin. Te nagłówki nie są standardem TinkerPop.

## <a name="headers"></a>Nagłówki

| Nagłówek | Typ | Przykładowa wartość | Po dołączeniu | Wyjaśnienie |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11,3243 | Sukces i niepowodzenie | Ilość przepływności kolekcji lub bazy danych wykorzystywana w [jednostkach żądania (ru/s lub jednostek ru)](request-units.md) dla komunikatu częściowego odpowiedzi. Ten nagłówek jest obecny w każdej kontynuacji dla żądań, które mają wiele fragmentów. Odzwierciedla opłaty za konkretny fragment odpowiedzi. Tylko w przypadku żądań składających się z pojedynczego fragmentu odpowiedzi ten nagłówek dopasowuje łączny koszt przechodzenia. Jednak w przypadku większości złożonych przechodzenia ta wartość reprezentuje koszt częściowy. |
| **x-ms-total-request-charge** | double | 423,987 | Sukces i niepowodzenie | Ilość przepływności kolekcji lub bazy danych wykorzystywana w [jednostkach żądania (ru/s lub jednostek ru)](request-units.md) dla całego żądania. Ten nagłówek jest obecny w każdej kontynuacji dla żądań, które mają wiele fragmentów. Wskazuje ona łączną opłatę od początku żądania. Wartość tego nagłówka w ostatnim fragmencie wskazuje na całkowitą opłatą żądania. |
| **x-MS-Server-Time-MS** | double | 13,75 | Sukces i niepowodzenie | Ten nagłówek jest uwzględniany w celu rozwiązywania problemów z opóźnieniem. Wskazuje on czas (w milisekundach), jaki program Cosmos DB Gremlin Server wykonał i wygenerował komunikat częściowej odpowiedzi. Użycie wartości tego nagłówka i porównanie jej z ogólnymi aplikacjami opóźnienia żądań może obliczyć obciążenie opóźnienia sieci. |
| **x-MS-Total-serwer-czas-MS** | double | 130,512 | Sukces i niepowodzenie | Łączny czas (w milisekundach), jaki program Cosmos DB Gremlin Server wykonał cały przechodzenie. Ten nagłówek jest uwzględniany w każdej częściowej odpowiedzi. Reprezentuje łączny czas wykonania od momentu rozpoczęcia żądania. Ostatnia odpowiedź wskazuje łączny czas wykonywania. Ten nagłówek jest przydatny do rozróżniania między klientem a serwerem jako źródłem opóźnienia. Można porównać czas wykonywania przechodzenia na kliencie z wartością tego nagłówka. |
| **x-ms-status-code** | długi | 200 | Sukces i niepowodzenie | Nagłówek wskazuje wewnętrzny powód uzupełniania lub kończenia żądania. Zalecane jest, aby aplikacja poszukiwała wartości tego nagłówka i podejmować działania naprawcze. |
| **x-MS-Substatus — kod** | długi | 1003 | Tylko Niepowodzenie | Cosmos DB to wielomodelowa baza danych, która jest oparta na ujednoliconej warstwie magazynu. Ten nagłówek zawiera dodatkowe informacje o przyczynie niepowodzenia w przypadku wystąpienia błędu w niższych warstwach stosu o wysokiej dostępności. Aplikacja zaleca przechowywanie tego nagłówka i używanie go podczas kontaktowania się z działem obsługi klienta Cosmos DB. Wartość tego nagłówka jest przydatna dla Cosmos DB inżyniera w celu szybkiego rozwiązywania problemów. |
| **x-ms-retry-after-ms** | ciąg (TimeSpan) | "00:00:03.9500000" | Tylko Niepowodzenie | Ten nagłówek jest reprezentacją ciągu typu [TimeSpan](/dotnet/api/system.timespan) platformy .NET. Ta wartość zostanie uwzględniona tylko w żądaniach zakończonych niepowodzeniem z powodu wyczerpania przepływności. Aplikacja powinna ponownie przesłać przechodzenie po upływie pożądanego czasu. |
| **x-ms-activity-id** | ciąg (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Sukces i niepowodzenie | Nagłówek zawiera unikatowy identyfikator po stronie serwera dla żądania. Każde żądanie ma przypisany unikatowy identyfikator serwera do celów śledzenia. Aplikacje powinny rejestrować identyfikatory działań zwracanych przez serwer w przypadku żądań, do których klienci mogą chcieć skontaktować się z pomocą techniczną. Pracownicy pomocy technicznej Cosmos DB mogą znaleźć określone żądania według identyfikatorów w usłudze Cosmos DB telemetrii usługi. |

## <a name="status-codes"></a>Kody stanu

Poniżej wymieniono najbardziej typowe kody zwracane dla `x-ms-status-code` atrybutu stanu.

| Stan | Wyjaśnienie |
| --- | --- |
| **401** | Komunikat o błędzie `"Unauthorized: Invalid credentials provided"` jest zwracany, jeśli hasło uwierzytelniania nie jest zgodne z kluczem konta Cosmos DB. Przejdź do konta Cosmos DB Gremlin w Azure Portal i upewnij się, że klucz jest prawidłowy.|
| **404** | Współbieżne operacje, które próbują usunąć i zaktualizować tę samą krawędź lub wierzchołek jednocześnie. Komunikat o błędzie `"Owner resource does not exist"` wskazuje, że określona baza danych lub kolekcja jest niepoprawna w ramach parametrów połączenia w formacie `/dbs/<database name>/colls/<collection or graph name>`.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Taka sytuacja zwykle występuje, gdy wierzchołek lub krawędź z danym identyfikatorem już istnieje w grafie.| 
| **412** | Kod stanu jest uzupełniony komunikatem o błędzie `"PreconditionFailedException": One of the specified pre-condition is not met` . Ten błąd jest indykatywny dla optymistycznego naruszenia kontroli współbieżności między odczytaniem krawędzi lub wierzchołkiem i zapisem go z powrotem do magazynu po modyfikacji. Najczęstsze sytuacje, w których ten błąd występuje w przypadku modyfikacji właściwości, na przykład `g.V('identifier').property('name','value')` . Aparat Gremlin odczyta wierzchołek, zmodyfikuje go i zapisze ponownie. Jeśli inne przechodzenie działa równolegle, próba zapisania tego samego wierzchołka lub krawędzi spowoduje wystąpienie tego błędu. Aplikacja powinna ponownie przesłać przechodzenie na serwer.| 
| **429** | Żądanie zostało ograniczone i powinno być ponawiane po wartości w **x-MS-retry-After-MS**| 
| **500** | Komunikat o błędzie zawierający informację `"NotFoundException: Entity with the specified id does not exist in the system."` wskazuje, że utworzono ponownie bazę danych i/lub kolekcję z tą samą nazwą. Ten błąd zniknie w ciągu 5 minut, gdy zmiana zostanie rozpropagowana i unieważni pamięci podręczne w różnych składnikach usługi Cosmos DB. Aby uniknąć tego problemu, za każdym razem używaj unikatowych nazw baz danych i kolekcji.| 
| **1000** | Ten kod stanu jest zwracany, gdy serwer pomyślnie przeanalizuje komunikat, ale nie można go wykonać. Zwykle wskazuje problem z kwerendą.| 
| **1001** | Ten kod jest zwracany, gdy serwer kończy wykonywanie przechodzenia, ale nie może serializować odpowiedzi z powrotem do klienta. Ten błąd może wystąpić, gdy przechodzenie generuje skomplikowany wynik, który jest zbyt duży lub nie jest zgodny ze specyfikacją protokołu TinkerPop. Aplikacja powinna uprościć przechodzenie po napotkaniu tego błędu. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` jest zwracany, gdy przechodzenie przekracza dozwolony limit pamięci. Limit pamięci wynosi **2 GB** na przechodzenie.| 
| **1004** | Ten kod stanu wskazuje źle sformułowane żądanie grafu. Żądanie może być nieprawidłowo sformułowane, gdy nie powiedzie się deserializacja, typ inny niż wartość jest deserializowany jako typ wartości lub zażądano nieobsługiwanej operacji Gremlin. Aplikacja nie powinna ponowić próby żądania, ponieważ nie powiodło się. | 
| **1007** | Zazwyczaj ten kod stanu jest zwracany z komunikatem o błędzie `"Could not process request. Underlying connection has been closed."` . Taka sytuacja może wystąpić, jeśli sterownik klienta próbuje użyć połączenia, które jest zamykane przez serwer. Aplikacja powinna ponowić próbę przechodzenia przy użyciu innego połączenia.
| **1008** | Cosmos DB Gremlin serwer może przerwać połączenia, aby ponownie zrównoważyć ruch w klastrze. Sterowniki klientów powinny obsługiwać tę sytuację i używać tylko połączeń na żywo do wysyłania żądań do serwera. Sporadyczne sterowniki klienta nie mogą wykryć, czy połączenie zostało zamknięte. Gdy aplikacja napotka błąd, `"Connection is too busy. Please retry after sometime or open more connections."` powinien ponowić próbę przechodzenia na inne połączenie.
| **1009** | Operacja nie została ukończona w wyznaczonym czasie i została anulowana przez serwer. Zoptymalizuj przechodzenie, aby szybko pracować przez filtrowanie wierzchołków lub krawędzi w każdym przechodzeniu do zakresu wyszukiwania wąskiego. Wartość domyślna limitu czasu żądania to **60 sekund**. |

## <a name="samples"></a>Samples

Przykładowa aplikacja kliencka oparta na Gremlin.Net, która odczytuje jeden atrybut stanu:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Przykład demonstrujący sposób odczytywania atrybutu stanu z Gremlin klienta Java:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Następne kroki
* [Kody stanu HTTP dla Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Nagłówki odpowiedzi REST Common Azure Cosmos DB](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Wymagania dostawcy sterownika programu TinkerPop Graph]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
