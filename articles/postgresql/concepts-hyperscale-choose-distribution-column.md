---
title: Wybieranie kolumn dystrybucji — skalowanie (Citus) — Azure Database for PostgreSQL
description: Dowiedz się, jak wybierać kolumny dystrybucji w typowych scenariuszach w Azure Database for PostgreSQL-Citus.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: f3c86f4b194e6d3935434b35cae958dfcf772986
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314916"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Wybieranie kolumn dystrybucji w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Wybór kolumny dystrybucji w poszczególnych tabelach jest jedną z najważniejszych decyzji, jakie podejmujesz podczas modelowania. Azure Database for PostgreSQL — funkcja Citus) przechowuje wiersze w fragmentów na podstawie wartości kolumny dystrybucji wierszy.

Poprawne grupy wyboru są powiązane z danymi w tych samych węzłach fizycznych, co sprawia, że zapytania są szybko dodawane i obsługują wszystkie funkcje SQL. Niepoprawny wybór sprawia, że system działa wolno i nie obsługuje wszystkich funkcji SQL w różnych węzłach.

Ten artykuł zawiera wskazówki dotyczące kolumn dystrybucji dla dwóch najpopularniejszych scenariuszy Citus.

### <a name="multi-tenant-apps"></a>Aplikacje z wieloma dzierżawami

Architektura wielu dzierżawców korzysta z formy modelowania hierarchicznej bazy danych w celu dystrybuowania zapytań między węzłami w grupie serwerów. Góra hierarchii danych jest określana jako *Identyfikator dzierżawy* i musi być przechowywana w kolumnie w każdej tabeli.

Funkcja wieloskalowania (Citus) sprawdza zapytania, aby zobaczyć, który identyfikator dzierżawy obejmuje i znajduje pasującą tabelę fragmentu. Kieruje zapytanie do jednego węzła procesu roboczego, który zawiera fragmentu. Uruchomienie zapytania ze wszystkimi odpowiednimi danymi umieszczonymi w tym samym węźle nosi nazwę wspólnej lokalizacji.

Poniższy diagram ilustruje współlokalizowanie w modelu danych wielodostępnych. Zawiera dwie tabele, konta i kampanie, z których każdy jest dystrybuowany przez program `account_id` . Zacienione pola reprezentują fragmentów. Zielona fragmentów są przechowywane razem w jednym węźle procesu roboczego, a niebieskie fragmentów są przechowywane w innym węźle procesu roboczego. Zwróć uwagę, jak zapytanie sprzężenia między kontami i kampaniami zawiera wszystkie niezbędne dane razem w jednym węźle, gdy obie tabele są ograniczone do tego samego \_ identyfikatora konta.

![Współlokalizacja z wieloma dzierżawcami](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Aby zastosować ten projekt we własnym schemacie, należy określić, co stanowi dzierżawcę w aplikacji. Typowe wystąpienia to firma, konto, organizacja lub klient. Nazwa kolumny będzie wyglądać następująco `company_id` lub `customer_id` . Sprawdź poszczególne zapytania i zadawaj siebie, czy będą one działały, jeśli miały dodatkowe klauzule WHERE, aby ograniczyć wszystkie tabele do wierszy z tym samym IDENTYFIKATORem dzierżawy?
Zapytania w modelu z wieloma dzierżawcami są objęte zakresem dzierżawy. Na przykład zapytania dotyczące sprzedaży lub spisu są objęte zakresem w ramach określonego magazynu.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Partycjonowanie tabel rozproszonych za pomocą wspólnej \_ kolumny identyfikatora dzierżawy.** Na przykład w aplikacji SaaS, w której znajdują się dzierżawcy są firmy, identyfikator dzierżawy \_ może być \_ identyfikatorem firmy.
-   **Konwertuj małe tabele obejmujące wiele dzierżawców na tabele referencyjne.** Gdy wiele dzierżawców udostępnia małą tabelę informacji, należy ją rozpowszechnić jako tabelę referencyjną.
-   **Ogranicz filtrowanie wszystkich zapytań aplikacji według identyfikatora dzierżawy \_ .** Każde zapytanie powinno zażądać informacji dla jednej dzierżawy w danym momencie.

Zapoznaj się z [samouczkiem z wieloma dzierżawcami](./tutorial-design-database-hyperscale-multi-tenant.md) , aby zapoznać się z przykładem tworzenia tego rodzaju aplikacji.

### <a name="real-time-apps"></a>Aplikacje w czasie rzeczywistym

Architektura wielu dzierżawców wprowadza strukturę hierarchiczną i używa wspólnej lokalizacji danych do kierowania zapytań na dzierżawcę. Natomiast architektury w czasie rzeczywistym zależą od konkretnych właściwości dystrybucji danych, aby osiągnąć wysoce równoległe przetwarzanie.

Używamy "identyfikatora jednostki" jako terminu dla kolumn dystrybucji w modelu czasu rzeczywistego. Typowe jednostki są użytkownikami, hostami lub urządzeniami.

Zapytania w czasie rzeczywistym zazwyczaj pytają o wartości zagregowane liczbowe pogrupowane według daty lub kategorii. Funkcja Citus) wysyła te zapytania do każdego fragmentuu w celu uzyskania częściowych wyników i składa ostateczną odpowiedź na węźle koordynatora. Zapytania działają najszybciej, gdy liczba węzłów jest możliwie jak najprawdopodobniej, a w przypadku, gdy żaden z pojedynczych węzłów nie musi wykonać nieproporcjonalnej ilości pracy.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Wybierz kolumnę o dużej kardynalności jako kolumnę dystrybucji.** W przypadku porównania pole Stan w tabeli zamówienia o wartościach nowe, płatne i wysłane jest niewłaściwym wyborem kolumny dystrybucji. Założono tylko kilka wartości, które ograniczają liczbę fragmentówów, które mogą zawierać dane, oraz liczbę węzłów, które mogą je przetworzyć. W kolumnach o dużej kardynalności warto również wybrać te kolumny, które są często używane w klauzulach Group by lub jako klucze sprzężenia.
-   **Wybierz kolumnę z parzystą dystrybucją.** W przypadku dystrybucji tabeli w kolumnie pochylonej do określonych wspólnych wartości dane w tabeli są gromadzone w pewnych fragmentów. Węzły, które przechowują te fragmentów, działają dłużej niż inne węzły.
-   **Rozpowszechnij tabele faktów i wymiarów w ich wspólnych kolumnach.**
    Tabela faktów może mieć tylko jeden klucz dystrybucji. Tabele, które dołączą do innego klucza nie będą wspólnie zlokalizowane z tabelą faktów. Wybierz jeden wymiar do rozłączenia w zależności od tego, jak często jest on połączony, i rozmiar wierszy sprzęgania.
-   **Zmień niektóre tabele wymiarów w tabele referencyjne.** Jeśli tabeli wymiarów nie można wspólnie zlokalizować z tabelą faktów, można poprawić wydajność zapytań, rozkładając kopie tabeli wymiarów na wszystkie węzły w postaci tabeli referencyjnej.

Zapoznaj się z [samouczkiem pulpitu nawigacyjnego w czasie rzeczywistym](./tutorial-design-database-hyperscale-realtime.md) , aby zapoznać się z przykładem tworzenia tego rodzaju aplikacji.

### <a name="time-series-data"></a>Dane szeregów czasowych

W obciążeniu szeregów czasowych aplikacje wykonują zapytania dotyczące najnowszych informacji podczas archiwizowania starych informacji.

Najbardziej typowym błędem modelowania informacji o szeregach czasowych w ramach skalowania (Citus) jest użycie sygnatury czasowej jako kolumny dystrybucji. Rozkład wartości skrótu oparty na czasie rozkłada się przypadkowo na różne fragmentów, zamiast przechowywać zakresy czasu razem w fragmentów. Zapytania, które obejmują czas na ogół odwołują się do zakresu czasu, na przykład najnowsze dane. Ten typ rozkładu mieszania prowadzi do obciążenia sieci.

#### <a name="best-practices"></a>Najlepsze rozwiązania

-   **Nie wybieraj sygnatury czasowej jako kolumny dystrybucji.** Wybierz inną kolumnę dystrybucji. W aplikacji z wieloma dzierżawami Użyj identyfikatora dzierżawy lub w aplikacji w czasie rzeczywistym Użyj identyfikatora jednostki.
-   **Zamiast tego użyj partycjonowania tabeli PostgreSQL.** Partycjonowanie tabeli służy do dzielenia dużej ilości danych uporządkowanych według czasu na wiele dziedziczonych tabel każdej tabeli zawierającej różne zakresy czasu. Dystrybuowanie tabeli partycjonowanej Postgres w Citus) tworzy fragmentów dla dziedziczonych tabel.

Zapoznaj się z [samouczkiem dotyczącym szeregów czasowych](https://aka.ms/hyperscale-tutorial-timeseries) , aby zapoznać się z przykładem tworzenia tego rodzaju aplikacji.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się [, jak](concepts-hyperscale-colocation.md) współdziałanie między danymi rozproszonymi ułatwia wykonywanie zapytań.
