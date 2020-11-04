---
title: Modelowanie danych w Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Dowiedz się więcej na temat modelowania danych w bazach danych NoSQL, różnice między danymi modelowania w relacyjnej bazie danych i bazą danych dokumentów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a141177846def9c94216684c1083d0d336eeda1e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333260"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelowanie danych w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Bazy danych bez schematu, takie jak Azure Cosmos DB, ułatwiają przechowywanie i wykonywanie zapytań dotyczących danych niestrukturalnych i z częściową strukturą, należy poświęcać trochę czasu na zaplanowanie modelu danych w celu uzyskania optymalnej wydajności i skalowalności oraz najniższych kosztów.

Jak są przechowywane dane? Jak aplikacja będzie pobierać dane i wysyłać do nich zapytania? Czy aplikacja jest odczytana i intensywnie zapisu?

Po przeczytaniu tego artykułu będzie można odpowiedzieć na następujące pytania:

* Co to jest Modelowanie danych i dlaczego warto zadbać o to?
* Jak dane modelowania w Azure Cosmos DB różnić się do relacyjnej bazy danych?
* Jak mogęe relacje danych ekspresowych w nierelacyjnej bazie danych?
* Kiedy można osadzać dane i jak łączyć się z danymi?

## <a name="embedding-data"></a>Osadzanie danych

Po rozpoczęciu modelowania danych w Azure Cosmos DB spróbuj traktować swoje jednostki jako **elementy samodzielne** reprezentowane jako dokumenty JSON.

Na potrzeby porównania najpierw zobaczymy, jak możemy modelować dane w relacyjnej bazie danych. Poniższy przykład pokazuje, jak osoba może być przechowywana w relacyjnej bazie danych.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Model relacyjnej bazy danych" border="false":::

W przypadku pracy z relacyjnymi bazami danych strategia ma na celu normalizację wszystkich danych. Normalizacja danych zazwyczaj obejmuje przejęcie jednostki, takiej jak osoba, i przedzielenie jej na składniki dyskretne. W powyższym przykładzie osoba może mieć wiele rekordów szczegółów kontaktu, a także wiele rekordów adresów. Szczegóły kontaktu można podzielić na dalsze wyodrębnianie wspólnych pól, takich jak typ. To samo dotyczy adresu, każdy rekord może być typu *dom* lub *Business*.

Identyfikator GUID w przypadku normalizacji danych ma na celu **uniknięcie przechowywania nadmiarowych danych** w każdym rekordzie i raczej odnieść się do danych. W tym przykładzie w celu odczytania osoby z uwzględnieniem wszystkich informacji kontaktowych i adresów należy użyć sprzężeń, aby efektywnie redagować (lub denormalizować) dane w czasie wykonywania.

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Aktualizowanie pojedynczej osoby przy użyciu ich szczegółów i adresów kontaktowych wymaga operacji zapisu w wielu pojedynczych tabelach.

Teraz przyjrzyjmy się sposobom, w jaki będziemy modelować te same dane jak samodzielna jednostka w Azure Cosmos DB.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

Korzystając z powyższego podejścia, odnosząc **się do** rekordu osoby, **osadzając** wszystkie informacje dotyczące tej osoby, takie jak informacje kontaktowe i adresy, w *jednym dokumencie JSON* .
Ponadto, ponieważ nie ograniczamy się do stałego schematu, mamy elastyczną możliwość wykonywania takich czynności, jak posiadanie szczegółowych informacji o różnych kształtach.

Pobranie kompletnego rekordu osoby z bazy danych jest teraz **jedną operacją odczytu** dla jednego kontenera i dla pojedynczego elementu. Aktualizacja rekordu osoby przy użyciu ich szczegółów i adresów kontaktowych jest również **jedną operacją zapisu** w odniesieniu do pojedynczego elementu.

Przez denormalizację danych, aplikacja może potrzebować mniejszej liczby zapytań i aktualizacji w celu wykonania typowych operacji.

### <a name="when-to-embed"></a>Kiedy należy osadzić

Ogólnie rzecz biorąc, użyj osadzonych modeli danych w programie:

* **Istnieją relacje między** jednostkami.
* Między jednostkami istnieją relacje **"jeden do kilku** ".
* Dane osadzone są **rzadko zmieniane**.
* Istnieją osadzone dane, które nie zostaną powiększone **bez powiązania**.
* Istnieje osadzona Data, do której często wykonywane są **zapytania**.

> [!NOTE]
> Zwykle nieznormalizowane modele danych zapewniają lepszą wydajność **odczytu** .

### <a name="when-not-to-embed"></a>Kiedy nie można osadzić

Chociaż reguła kciuka w Azure Cosmos DB ma denormalizować wszystko i osadzić wszystkie dane w jednym elemencie, może to prowadzić do pewnych sytuacji, które należy unikać.

Zrób ten fragment kodu JSON.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

Może to być obiekt post z osadzonymi komentarzami, który będzie wyglądać podobnie w przypadku modelowania typowego bloga lub systemu CMS. Problem z tym przykładem polega na tym, że tablica komentarzy jest **nieograniczona** , co oznacza, że nie ma (praktyczne) limitu liczby komentarzy, które mogą mieć pojedynczy wpis. Może to stać się problemem, ponieważ rozmiar elementu może wzrosnąć nieskończonie.

Ponieważ rozmiar elementu zwiększa możliwość przesyłania danych za pośrednictwem sieci, a także odczytywanie i aktualizowanie elementu, na dużą skalę.

W takim przypadku lepszym rozwiązaniem jest uwzględnienie poniższego modelu danych.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Ten model ma trzy ostatnie komentarze osadzone w kontenerze post, który jest tablicą ze stałym zestawem atrybutów. Inne Komentarze są pogrupowane w do partii 100 komentarzy i przechowywane jako oddzielne elementy. Rozmiar partii został wybrany jako 100, ponieważ nasza fikcyjna aplikacja umożliwia użytkownikowi ładowanie 100 komentarzy w danym momencie.  

Innym przypadkiem, gdy osadzenie danych nie jest dobrym pomysłem, jest to, że osadzone dane są często używane między elementami i zmieniają się często.

Zrób ten fragment kodu JSON.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

Może to reprezentować portfolio magazynowego osoby. Wybrano osadzenie informacji o zapasach w każdym dokumencie portfolio. W środowisku, w którym dane związane ze zmianą są często zmieniane, podobnie jak aplikacja do notowania giełdowego, osadzenie danych, które często zmieniają się, to oznacza, że stale aktualizujesz każdy dokument portfolio przy każdej wymianie zasobów.

*Zaza* giełdowe mogą być sprzedawane wiele razy w jednym dniu, a tysiące użytkowników mogą mieć *Zaza* w portfelu. Korzystając z modelu danych, takiego jak powyżej, będziemy musieli aktualizować wiele tysięcy dokumentów portfolio wiele razy dziennie, co pozwoli na system, który nie jest dobrze skalowany.

## <a name="referencing-data"></a>Odwołujące się do danych

Osadzanie danych działa dobrze w wielu przypadkach, ale istnieją scenariusze, w których denormalizacja danych spowoduje więcej problemów niż jest to możliwe. Co teraz robimy?

Relacyjne bazy danych nie są jedynym miejscem, w którym można tworzyć relacje między jednostkami. W bazie danych dokumentów można zawierać informacje w jednym dokumencie, które odnoszą się do danych w innych dokumentach. Nie zaleca się kompilowania systemów, które lepiej nadają się do relacyjnej bazy danych w Azure Cosmos DB lub innej bazy danych dokumentów, ale proste relacje są bardziej przydatne i mogą być użyteczne.

W poniższym kodzie JSON wybieramy użycie przykładu portfela giełdowego z wcześniejszego, ale tym razem odwołujesz się do elementu giełdowego w portfolio, zamiast go osadzać. W ten sposób, gdy element giełdowy ulega częstym zmianom w ciągu dnia, jedynym dokumentem, który ma zostać zaktualizowany, jest ten, który należy zaktualizować.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

Natychmiast minusem tego podejścia, jeśli aplikacja jest wymagana do wyświetlania informacji o każdym magazynie, który jest przechowywany podczas wyświetlania portfolio osoby; w takim przypadku należy wykonać wiele podróży do bazy danych w celu załadowania informacji dla każdego dokumentu giełdowego. Tutaj podjęto decyzję o ulepszaniu wydajności operacji zapisu, która zdarza się często w ciągu dnia, ale z powodu naruszenia operacji odczytu, które potencjalnie mają mniejszy wpływ na wydajność danego systemu.

> [!NOTE]
> Znormalizowane modele danych **mogą wymagać większej liczby rund** do serwera.

### <a name="what-about-foreign-keys"></a>Co o kluczach obcych?

Ponieważ obecnie nie ma koncepcji ograniczenia, klucza obcego lub w inny sposób, wszelkie relacje między dokumentami, które znajdują się w dokumentach, są skutecznie "słabymi łączami" i nie będą weryfikowane przez samą bazę danych. Jeśli chcesz mieć pewność, że dane, do których odwołuje się dokument, rzeczywiście istnieją, należy to zrobić w aplikacji lub przy użyciu wyzwalaczy po stronie serwera lub procedur składowanych w Azure Cosmos DB.

### <a name="when-to-reference"></a>Kiedy można się odwołać

Ogólnie rzecz biorąc, używaj znormalizowanych modeli danych, gdy:

* Reprezentuje relacje **jeden do wielu** .
* Reprezentuje relacje **wiele do wielu** .
* Często związane ze sobą **zmiany** danych.
* Dane, do których istnieją odwołania, mogą być **niepowiązane**.

> [!NOTE]
> Zwykle normalizacja zapewnia lepszą wydajność **zapisu** .

### <a name="where-do-i-put-the-relationship"></a>Gdzie mogę umieścić relację?

Wzrost relacji pomoże określić, w którym dokumencie jest przechowywane odwołanie.

Jeśli przeszukasz Poniższy kod JSON, ten modeluje wydawców i książki.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Jeśli liczba ksiąg na wydawcę jest mała z ograniczonym wzrostem, może być przydatne przechowywanie odwołania do książki wewnątrz dokumentu wydawcy. Jeśli jednak liczba książek dla wydawcy jest nieograniczona, wówczas ten model danych prowadziłoby do modyfikowalnych, rosnących tablic, jak w przypadku przykładowego dokumentu wydawcy powyżej.

Przełączenie na bit spowoduje powstanie modelu, który nadal reprezentuje te same dane, ale teraz pozwala uniknąć tych dużych niemodyfikowalnych kolekcji.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

W powyższym przykładzie podaliśmy niepowiązaną kolekcję do dokumentu wydawcy. Zamiast tego mamy już odwołanie do wydawcy w każdym dokumencie książki.

### <a name="how-do-i-model-manymany-relationships"></a>Jak mogę model wiele: wiele relacji?

W relacyjnej bazie danych *wiele: wiele* relacji jest często modelowanych przy użyciu tabel sprzężenia, które po prostu łączą rekordy z innych tabel.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Sprzęganie tabel" border="false":::

Może być skłonny do replikowania tych samych rzeczy przy użyciu dokumentów i tworzenia modelu danych, który wygląda podobnie do poniższego.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

To będzie działała. Jednak załadowanie autora przy użyciu ich ksiąg lub załadowanie książki z jej autorem zawsze wymaga co najmniej dwóch dodatkowych zapytań względem bazy danych. Jedno zapytanie do przyłączania dokumentu, a następnie inne zapytanie w celu pobrania rzeczywistego dokumentu.

Jeśli cała ta tabela sprzężenia jest taka, gluing dwie fragmenty danych, a następnie dlaczego nie porzucasz jej całkowicie?
Weź pod uwagę następujące kwestie.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Teraz, jeśli mam autora, natychmiast wiemy, które książki zostały napisane, i odwrotnie, jeśli mam załadowana dokument książki, znamy identyfikatory autorów. Spowoduje to zaoszczędzenie zapytania pośredniego względem tabeli sprzężenia, zmniejszając liczbę operacji w sieci, które musi wykonać aplikacja.

## <a name="hybrid-data-models"></a>Hybrydowe modele danych

Teraz wykorzystamy osadzenie (lub denormalizowanie) i odwołujące się do nich (lub normalizowanie) danych, każdy z nich ma swoje boki i każdy z nich ma naruszone kompromisy.

Nie zawsze musi być albo lub, nie być obawialiśmy, aby można było nieco nie mieszać.

W oparciu o specyficzne dla aplikacji wzorce użycia i obciążenia mogą wystąpić sytuacje, w których mieszanie osadzonych i przywoływanych danych jest sensowne i może prowadzić do uproszczenia logiki aplikacji z mniejszą liczbą podróży serwera przy zachowaniu dobrego poziomu wydajności.

Rozważmy poniższy kod JSON.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

W tym miejscu (głównie) nastąpiło osadzony model, w którym dane z innych jednostek są osadzone w dokumencie najwyższego poziomu, ale istnieją odwołania do innych danych.

Jeśli zobaczysz dokument księgi, zobaczymy kilka interesujących pól, gdy zobaczymy tablicę autorów. Istnieje `id` pole, które jest używane do odwoływania się z powrotem do dokumentu autora, standardowej zwyczajowej w znormalizowanym modelu, ale również mamy `name` i `thumbnailUrl` . Firma Microsoft mogła zostać zablokowana `id` i pozostawiona aplikacji w celu uzyskania wszelkich dodatkowych informacji wymaganych od odpowiedniego dokumentu autora przy użyciu linku, ale ponieważ nasza aplikacja wyświetla nazwę autora i miniaturę obrazu z każdą z wyświetlonych książek, można zapisać w postaci okrężnej na serwerze dla każdej książki na liście, denormalizacjjąc **niektóre** dane od autora.

Upewnij się, że jeśli nazwa autora została zmieniona lub chcesz zaktualizować swoje zdjęcie, musimy przeszedł i zaktualizować każdą opublikowaną przez siebie książkę, ale dla naszej aplikacji, w oparciu o założenie, że autorzy nie zmieniają nazw często, jest to akceptowalna decyzja w zakresie projektu.  

W przykładzie istnieją **wstępnie obliczone wartości zagregowane** , aby zaoszczędzić kosztowne przetwarzanie operacji odczytu. W przykładzie niektóre dane osadzone w dokumencie autor są danymi obliczanymi w czasie wykonywania. Za każdym razem, gdy nowa książka jest publikowana, tworzony jest dokument księgi, **a** pole countOfBooks jest ustawione na wartość obliczoną na podstawie liczby dokumentów księgi istniejących dla danego autora. Ta optymalizacja jest lepsza w odniesieniu do dużych systemów, w których możemy umożliwić wykonywanie obliczeń przy zapisach w celu zoptymalizowania operacji odczytu.

Istnieje możliwość, że model z polami wstępnie obliczonymi jest możliwy, ponieważ Azure Cosmos DB obsługuje **transakcje wielodokumentowe**. Wiele magazynów NoSQL nie może wykonywać transakcji między dokumentami i dlatego ambasadoruje decyzje projektowe, takie jak "Zawsze osadzaj wszystko", z powodu tego ograniczenia. Za pomocą Azure Cosmos DB można używać wyzwalaczy po stronie serwera lub procedur składowanych, które wstawiają książki i aktualizują autorów w ramach transakcji KWAŚNej. Teraz nie **trzeba** osadzać wszystkiego w jednym dokumencie, aby upewnić się, że dane pozostają spójne.

## <a name="distinguishing-between-different-document-types"></a>Rozróżnianie między różnymi typami dokumentów

W niektórych scenariuszach warto mieszać różne typy dokumentów w tej samej kolekcji. zwykle jest to przypadek, gdy wiele powiązanych dokumentów ma być w tej samej [partycji](partitioning-overview.md). Można na przykład umieścić zarówno książki, jak i książki, w tej samej kolekcji, a następnie podzielić je na partycje `bookId` . W takiej sytuacji zazwyczaj warto dodać do dokumentów za pomocą pola, które identyfikuje ich typ w celu odróżnienia ich.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Następne kroki

Największą wnioskią z tego artykułu jest zrozumienie, że Modelowanie danych w świecie wolnym od schematu jest tak ważne jak kiedykolwiek.

Tak samo jak nie istnieje jeden sposób reprezentowania fragmentu danych na ekranie, nie istnieje jeden sposób na Modelowanie danych. Musisz zrozumieć swoją aplikację i sposób, w jaki będzie ona generować, wykorzystywać i przetwarzać dane. Następnie stosując niektóre z wytycznych przedstawionych w tym miejscu, można skonfigurować model, który odnosi się do natychmiastowych potrzeb aplikacji. Gdy aplikacje wymagają zmiany, można wykorzystać elastyczność bazy danych bez schematu, aby wdrożyć tę zmianę i łatwo rozwijać model danych.

Aby dowiedzieć się więcej na temat Azure Cosmos DB, zapoznaj się ze stroną [dokumentacji](https://azure.microsoft.com/documentation/services/cosmos-db/) usługi.

Aby dowiedzieć się, jak fragmentu dane w wielu partycjach, zobacz [Partycjonowanie danych w Azure Cosmos DB](partitioning-overview.md).

Aby dowiedzieć się, jak modelować i dzielić dane na Azure Cosmos DB przy użyciu rzeczywistego przykładu, zapoznaj się z artykułem [ modelowanie i partycjonowanie danych — Real-World przykład](how-to-model-partition-example.md).
