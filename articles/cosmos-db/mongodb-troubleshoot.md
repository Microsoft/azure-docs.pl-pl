---
title: Rozwiązywanie typowych błędów w interfejsie API Azure Cosmos DB dla usługi Mongo DB
description: W tym dokumencie omówiono sposoby rozwiązywania typowych problemów napotykanych w interfejsie API Azure Cosmos DB MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 13ac90ae70262f2f6781f5f40ec67da4bf74ab68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661277"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W poniższym artykule opisano typowe błędy i rozwiązania dla wdrożeń za pomocą interfejsu API Azure Cosmos DB dla MongoDB.

>[!Note]
> Azure Cosmos DB nie hostuje aparatu MongoDB. Zapewnia implementację protokołu MongoDB Wire w [wersji 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)i starszej obsłudze [protokołu przewodowego w wersji 3,2](mongodb-feature-support.md). W związku z tym niektóre z tych błędów są dostępne tylko w interfejsie API Azure Cosmos DB dla MongoDB.

## <a name="common-errors-and-solutions"></a>Typowe błędy i rozwiązania

| Kod       | Błąd                | Opis  | Rozwiązanie  |
|------------|----------------------|--------------|-----------|
| 2 | Ścieżka indeksu odpowiadająca określonemu elementowi order-by jest wykluczona lub zapytanie order by nie ma odpowiedniego indeksu złożonego, z którego może być obsługiwane. | Zapytanie żąda sortowania w nieindeksowanym polu. | Utwórz indeks (lub indeks złożony) pasujący do zapytania sortowania, które próbujesz utworzyć. |
| 13 | Brak autoryzacji | Żądanie nie ma uprawnień do ukończenia. | Upewnij się, że ustawisz odpowiednie uprawnienia dla bazy danych i kolekcji.  |
| 16 | InvalidLength | Określone żądanie ma nieprawidłową długość. | Jeśli używasz funkcji Wyjaśnij (), upewnij się, że podasz tylko jedną operację. |
| 26 | NamespaceNotFound | Nie można odnaleźć bazy danych lub kolekcji, do której odwołuje się zapytanie. | Upewnij się, że nazwa bazy danych/kolekcji dokładnie pasuje do nazwy w zapytaniu.|
| 50 | ExceededTimeLimit | Żądanie przekroczyło limit czasu wykonywania wynoszący 60 sekund. |  Może istnieć wiele przyczyn tego błędu. Jedną z przyczyn może być fakt, że obecnie przypisana wydajność jednostek żądania może być niedostateczna, aby ukończyć żądanie. Ten problem można rozwiązać przez zwiększenie liczby jednostek żądania dla tej kolekcji lub bazy danych. W innych przypadkach ten błąd można obejść, dzieląc duże żądanie na mniejsze. Ponowienie próby wykonania operacji zapisu, w której wystąpił ten błąd, może spowodować powstanie duplikatu zapisu. <br><br>Jeśli próbujesz usunąć duże ilości danych bez wpływu na jednostki RU: <br>-Rozważ użycie czasu wygaśnięcia (w oparciu o sygnaturę czasową): [wygasają dane za pomocą interfejsu API Azure Cosmos DB MongoDB](./mongodb-time-to-live.md) <br>— Użyj rozmiaru kursora/partii, aby wykonać operację usuwania. Możesz pobierać jeden dokument na raz i usuwać go w pętli. Umożliwi to powolne wykonanie usunięcia danych bez wpływu na aplikację produkcyjną.|
| 61 | ShardKeyNotFound | Dokument w żądaniu nie zawiera klucza partycjonującego kolekcji (klucza partycji usługi Azure Cosmos DB). | Upewnij się, że klucz partycjonujący kolekcji jest używany w danym żądaniu.|
| 66 | ImmutableField | Żądanie próbuje zmienić niezmienne pole | pola "ID" są niezmienne. Upewnij się, że Twoje żądanie nie próbuje zaktualizować tego pola. |
| 67 | CannotCreateIndex | Nie można ukończyć żądania utworzenia indeksu. | W kontenerze można utworzyć maksymalnie 500 indeksów pojedynczego pola. W indeksie złożonym można uwzględnić maksymalnie osiem pól (indeksy złożone są obsługiwane w wersji 3.6+). |
| 115 | CommandNotSupported | Podjęte żądanie nie jest obsługiwane. | Dodatkowe szczegóły powinny zostać podane w błędzie. Jeśli ta funkcja jest ważna dla wdrożeń, daj nam znać, tworząc bilet pomocy technicznej w [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | Klucz partycjonujący (klucz partycji usługi Azure Cosmos DB) wstawianego dokumentu już istnieje w kolekcji lub naruszono ograniczenie pola indeksu unikatowego. | Użyj funkcji update(), aby zaktualizować istniejący dokument. Jeśli ograniczenie pola indeksu unikatowego zostało naruszone, wstaw lub zaktualizuj dokument przy użyciu wartości pola, która nie istnieje jeszcze w kluczu partycjonującym/partycji. |
| 16500 | TooManyRequests  | Łączna liczba użytych jednostek żądania jest większa niż aprowizowana prędkość jednostek żądania dla tej kolekcji i zostało zastosowane ograniczanie. | Rozważ skalowanie przepływności przypisanej do kontenera lub zestawu kontenerów w witrynie Azure Portal albo spróbuj wykonać operację ponownie. Jeśli [włączysz serwer SSR](prevent-rate-limiting-errors.md) (ponowienie po stronie serwera), Azure Cosmos DB automatycznie ponawia próbę żądania, które się nie powiodły z powodu tego błędu. |
| 16501 | ExceededMemoryLimit | W przypadku usługi wielodostępnej operacja przekroczyła przydział pamięci klienta. | Zmniejsz zakres operacji, stosując bardziej restrykcyjne kryteria zapytania, lub skontaktuj się z pomocą techniczną w witrynie [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Przykład: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Nierozpoznana nazwa etapu potoku. | Nie rozpoznano nazwy etapu w żądaniu potoku agregacji. | Upewnij się, że wszystkie nazwy potoków agregacji w żądaniu są prawidłowe. |
| - | Problemy dotyczące wersji protokołu Wire bazy danych MongoDB | Starsze wersje sterowników bazy danych MongoDB nie są w stanie wykryć nazwy konta usługi Azure Cosmos w parametrach połączenia. | Dołącz *nazwa_aplikacji = @**AccountName** @* na końcu interfejsu API Cosmos DB dla parametrów połączenia MongoDB, gdzie ***AccountName*** jest nazwą konta Cosmos DB. |
| - | Problemy dotyczące sieci klienta bazy danych MongoDB (takie jak wyjątki socket lub endOfStream)| Żądanie sieci zakończyło się niepowodzeniem. Jest to często powodowane nieaktywnym połączeniem TCP, którego próbuje użyć klient bazy danych MongoDB. Sterowniki bazy danych MongoDB często wykorzystują buforowanie połączeń, czego wynikiem jest losowe połączenie wybrane z puli używanej dla żądania. Nieaktywne połączenia zwykle kończą się przekroczeniem limitu czasu w usłudze Azure Cosmos DB po upływie czterech minut. | Można ponowić próbę wykonania tych nieudanych żądań w kodzie aplikacji, zmienić ustawienia klienta MongoDB (sterowniki) tak, aby usuwania nieaktywne połączenia protokołu TCP przed upływem czterech minut limitu czasu lub skonfigurować ustawienia utrzymania aktywności systemu operacyjnego w celu utrzymywania połączeń TCP w stanie aktywnym.<br><br>Aby uniknąć komunikatów o łączności, można zmienić parametry połączenia, ustawiając parametr maxConnectionIdleTime na wartość 1-2 minuty.<br>-Mongo sterownik: Skonfiguruj *maxIdleTimeMS = 120000* <br>-Node.JS: Skonfiguruj *socketTimeoutMS = 120000*, *autoReconnect* = true, in = *true,* *keepAliveInitialDelay* = 3 minuty

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.