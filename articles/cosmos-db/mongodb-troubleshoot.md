---
title: Rozwiązywanie typowych błędów w interfejsie API Azure Cosmos DB dla usługi Mongo DB
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API Azure Cosmos DB MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258188"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W poniższym artykule opisano typowe błędy i rozwiązania dla wdrożeń za pomocą interfejsu API Azure Cosmos DB dla MongoDB.

>[!Note]
> Azure Cosmos DB nie hostuje aparatu MongoDB. Zapewnia implementację protokołu sieci MongoDB. W związku z tym niektóre z tych błędów są dostępne tylko w interfejsie API Azure Cosmos DB dla MongoDB. 

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Kod       | Błąd                | Opis  | Rozwiązanie  |
|------------|----------------------|--------------|-----------|
| 2 | Ścieżka indeksu odpowiadająca określonemu elementowi order-by jest wykluczona lub zapytanie order by nie ma odpowiedniego indeksu złożonego, z którego może być obsługiwane. | Zapytanie żąda sortowania w nieindeksowanym polu. | Utwórz zgodny indeks (lub indeks złożony) dla podjętych kwerend sortowania. |
| 13 | Brak autoryzacji | Żądanie nie ma uprawnień do ukończenia. | Upewnij się, że ustawisz odpowiednie uprawnienia dla bazy danych i kolekcji.  |
| 16 | InvalidLength | Określone żądanie ma nieprawidłową długość. | Jeśli używasz funkcji Wyjaśnij (), upewnij się, że podasz tylko jedną operację. |
| 26 | NamespaceNotFound | Nie można odnaleźć bazy danych lub kolekcji, do której odwołuje się zapytanie. | Upewnij się, że nazwa bazy danych/kolekcji dokładnie pasuje do nazwy w zapytaniu.|
| 50 | ExceededTimeLimit | Żądanie przekroczyło limit czasu wykonywania wynoszący 60 sekund. |  Może istnieć wiele przyczyn tego błędu. Jedną z przyczyn jest to, że aktualnie przydzieloną pojemność jednostki żądania nie jest wystarczająca do wykonania żądania. Ten problem można rozwiązać przez zwiększenie liczby jednostek żądania dla tej kolekcji lub bazy danych. W innych przypadkach ten błąd można obejść, dzieląc duże żądanie na mniejsze. Ponowienie operacji zapisu, która odebrała ten błąd może spowodować powstanie duplikatu zapisu. <br><br>Jeśli próbujesz usunąć duże ilości danych bez wpływu na jednostek ru: <br>-Rozważ użycie czasu wygaśnięcia (w oparciu o sygnaturę czasową): [wygasają dane za pomocą interfejsu API Azure Cosmos DB MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) <br>— Użyj rozmiaru kursora/partii, aby wykonać operację usuwania. Możesz pobierać jeden dokument na raz i usuwać go w pętli. Pomoże to powoli usunąć dane bez wpływu na aplikację produkcyjną.|
| 61 | ShardKeyNotFound | Dokument w żądaniu nie zawiera klucza fragmentu kolekcji (klucz partycji Azure Cosmos DB). | Upewnij się, że klucz fragmentu kolekcji jest używany w żądaniu.|
| 66 | ImmutableField | Żądanie próbuje zmienić niezmienne pole | pola "ID" są niezmienne. Upewnij się, że Twoje żądanie nie próbuje zaktualizować tego pola. |
| 67 | CannotCreateIndex | Nie można ukończyć żądania utworzenia indeksu. | W kontenerze można utworzyć maksymalnie 500 indeksów pojedynczego pola. Można uwzględnić maksymalnie osiem pól w indeksie złożonym (indeksy złożone są obsługiwane w wersji 3.6 +). |
| 115 | CommandNotSupported | Żądanie nie jest obsługiwane. | Dodatkowe szczegóły powinny zostać podane w błędzie. Jeśli ta funkcja jest ważna dla wdrożeń, daj nam znać, tworząc bilet pomocy technicznej w [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | Klucz fragmentu (klucz partycji Azure Cosmos DB) wstawianego dokumentu już istnieje w kolekcji lub naruszenie ograniczenia pola indeksu unikatowego. | Użyj funkcji Update (), aby zaktualizować istniejący dokument. Jeśli ograniczenie pola indeksu unikatowego zostało naruszone, Wstaw lub zaktualizuj dokument przy użyciu wartości pola, która nie istnieje jeszcze na fragmentu/partycji. |
| 16500 | TooManyRequests  | Łączna liczba użytych jednostek żądania jest większa niż aprowizowana prędkość jednostek żądania dla tej kolekcji i zostało zastosowane ograniczanie. | Rozważ skalowanie przepływności przypisanej do kontenera lub zestawu kontenerów w witrynie Azure Portal albo spróbuj wykonać operację ponownie. Jeśli [włączysz serwer SSR](prevent-rate-limiting-errors.md) (ponowienie po stronie serwera), Azure Cosmos DB automatycznie ponawia próbę żądania, które się nie powiodły z powodu tego błędu. |
| 16501 | ExceededMemoryLimit | W ramach usługi wielodostępnej Operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji przy użyciu bardziej restrykcyjnych kryteriów zapytania lub skontaktuj się z pomocą techniczną z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nierozpoznana Nazwa etapu potoku. | Nie rozpoznano nazwy etapu w żądaniu potoku agregacji. | Upewnij się, że wszystkie nazwy potoków agregacji są prawidłowe w żądaniu. |
| - | Problemy dotyczące wersji protokołu Wire bazy danych MongoDB | Starsze wersje sterowników MongoDB nie mogą wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia. | Dołącz *nazwa_aplikacji = @**AccountName** @* na końcu interfejsu API Cosmos DB dla parametrów połączenia MongoDB, gdzie ***AccountName** _ jest nazwą konta Cosmos DB. |
| - | MongoDB problemy sieciowe klienta (takie jak wyjątki Socket lub endOfStream)| Żądanie sieci zakończyło się niepowodzeniem. Jest to często spowodowane nieaktywnym połączeniem TCP, którego klient MongoDB próbuje użyć. Sterowniki MongoDB często wykorzystują pule połączeń, co powoduje losowe połączenie wybrane z puli używanej dla żądania. Nieaktywne połączenia zazwyczaj przekraczają limit czasu Azure Cosmos DB po upływie czterech minut. | Można ponowić próbę wykonania tych nieudanych żądań w kodzie aplikacji, zmienić ustawienia klienta MongoDB (sterowniki) tak, aby usuwania nieaktywne połączenia protokołu TCP przed upływem czterech minut limitu czasu lub skonfigurować ustawienia utrzymania aktywności systemu operacyjnego w celu utrzymywania połączeń TCP w stanie aktywnym.<br><br>Aby uniknąć komunikatów o łączności, można zmienić parametry połączenia, ustawiając parametr maxConnectionIdleTime na wartość 1-2 minuty.<br>-Mongo sterownik: Skonfiguruj _maxIdleTimeMS = 120000 * <br>-Node.JS: Skonfiguruj *socketTimeoutMS = 120000*, *autoReconnect* = true, in = *true,* *keepAliveInitialDelay* = 3 minuty

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
