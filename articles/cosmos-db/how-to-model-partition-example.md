---
title: Modelowanie i partycjonowanie danych na Azure Cosmos DB z rzeczywistym przykładem
description: Dowiedz się, jak modelować i dzielić na partycje rzeczywisty przykład przy użyciu podstawowego interfejsu API Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 7274627ccf0aaab29f3ca569568e0085d53f1dea
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818098"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Jak modelować i partycjonować dane w usłudze Azure Cosmos DB przy użyciu przykładu wziętego z życia

W tym artykule opisano kilka Azure Cosmos DB pojęć, takich jak [Modelowanie danych](modeling-data.md), [partycjonowanie](partitioning-overview.md)i [przepływność](request-units.md) , aby zademonstrować, jak można zrealizować rzeczywiste ćwiczenie projektowania danych.

Jeśli zwykle pracujesz z relacyjnymi bazami danych, prawdopodobnie skompilowano nawyki i intuitions na temat projektowania modelu danych. Ze względu na określone ograniczenia, ale również unikatowe siły Azure Cosmos DB, większość tych najlepszych rozwiązań nie jest dobrze przeważnie i może przeciągać Cię do nieoptymalnych rozwiązań. Celem tego artykułu jest przeprowadzenie przez cały proces modelowania rzeczywistych przypadków użycia na Azure Cosmos DB z modelowania elementów na potrzeby przenoszenia jednostek i partycjonowania kontenerów.

## <a name="the-scenario"></a>Scenariusz

W tym ćwiczeniu będziemy traktować domenę platformy do obsługi blogów, w której *Użytkownicy* mogą tworzyć *wpisy*. Użytkownicy *mogą również dodawać* *Komentarze* do tych wpisów.

> [!TIP]
> Niektóre słowa są wyróżnione *kursywą*. te słowa identyfikują rodzaj "rzeczy", które będą musiały manipulować naszym modelem.

Dodawanie większej liczby wymagań do naszej specyfikacji:

- Na pierwszej stronie jest wyświetlany kanał informacyjny ostatnio utworzonych wpisów,
- Możemy pobrać wszystkie wpisy dla użytkownika, wszystkie komentarze dla wpisu i wszystkie polubienia dla wpisu,
- Wpisy są zwracane wraz z nazwą użytkownika autorów oraz liczbą komentarzy i polubień,
- Komentarze i polubienia są również zwracane z nazwą użytkownika, którzy je utworzyły,
- Gdy są wyświetlane jako listy, wpisy muszą zawierać skrócone podsumowanie ich zawartości.

## <a name="identify-the-main-access-patterns"></a>Identyfikowanie głównych wzorców dostępu

Aby rozpocząć, firma Microsoft udostępnia pewną strukturę do wstępnej specyfikacji przez zidentyfikowanie wzorców dostępu rozwiązania. Projektując model danych dla Azure Cosmos DB, ważne jest, aby zrozumieć, które żądania musi spełniać nasz model, aby upewnić się, że model będzie obsługiwał te żądania efektywnie.

Aby uprościć cały proces, należy klasyfikować te różne żądania jako polecenia lub zapytania, a także zażyczać niektórych słownictwa od [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) , gdzie polecenia są żądaniami zapisu (to jest, intencje aktualizacji systemu) i zapytania są żądaniami tylko do odczytu.

Poniżej znajduje się lista żądań, które będą musiały uwidocznić nasze platformy:

- **[C1]** Tworzenie/edytowanie użytkownika
- **[Q1]** Pobierz użytkownika
- **[C2]** Utwórz/Edytuj wpis
- **[Q2]** Pobieranie wpisu
- **[Q3]** Wyświetlanie listy wpisów użytkownika w postaci krótkiej
- **[C3]** Utwórz komentarz
- **[4 kwartale]** Wyświetlanie listy komentarzy dotyczących wpisów
- **[C4]** Jak wpis
- **[Q5]** Wyświetlanie polubień wpisu
- **[Q6]** Lista *x* najnowszych wpisów utworzonych w formie krótkiej (Źródło danych)

Na tym etapie nie podaliśmy szczegółowych informacji o tym, co obejmuje każdy podmiot (użytkownik, wpis itp.). Ten krok zazwyczaj należy do pierwszej z nich, aby zostać zaprojektowany podczas projektowania względem relacyjnego magazynu, ponieważ musimy ustalić, w jaki sposób te jednostki zostaną przetłumaczone w postaci tabel, kolumn, kluczy obcych itp. Jest to znacznie mniejsze zagadnienie związane z bazą danych dokumentów, która nie wymusza żadnego schematu przy zapisie.

Głównym powodem, dlaczego ważne jest zidentyfikowanie wzorców dostępu od początku, jest to spowodowane tym, że ta lista żądań ma być naszym zestawem testów. Za każdym razem, gdy przejdziemy do iteracji nad modelem danych, wyślemy poszczególne żądania i sprawdza jego wydajność i skalowalność.

## <a name="v1-a-first-version"></a>V1: Pierwsza wersja

Zaczynamy od dwóch kontenerów: `users` i `posts` .

### <a name="users-container"></a>Kontener Użytkownicy

W tym kontenerze są przechowywane tylko elementy użytkownika:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Ten kontener jest podzielony przez `id` , co oznacza, że każda partycja logiczna w tym kontenerze będzie zawierać tylko jeden element.

### <a name="posts-container"></a>Kontener ogłoszeń

Ten kontener zawiera wpisy, komentarze i polubienia:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "title": "<post-title>",
    "content": "<post-content>",
    "creationDate": "<post-creation-date>"
}

{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "creationDate": "<like-creation-date>"
}
```

Ten kontener jest podzielony przez `postId` , co oznacza, że każda partycja logiczna w tym kontenerze będzie zawierać jeden wpis, a wszystkie komentarze dla tego wpisu i wszystkie polubienia dla tego wpisu.

Należy zauważyć, że wprowadzono `type` Właściwość w elementach przechowywanych w tym kontenerze w celu rozróżnienia między trzema typami jednostek, które są hostem tego kontenera.

Ponadto w przypadku wybrania odwołań do powiązanych danych zamiast osadzania ich (szczegółowe informacje o tych pojęciach można znaleźć w [tej sekcji](modeling-data.md) ), ponieważ:

- nie istnieje górny limit liczby ogłoszeń, które użytkownik może utworzyć,
- wpisy mogą być arbitralnie długie,
- nie ma górnego limitu liczby komentarzy i polubienia wpisu,
- chcemy mieć możliwość dodawania komentarza lub przypominania wpisu, bez konieczności aktualizowania samego wpisu.

## <a name="how-well-does-our-model-perform"></a>Jak dobrze działa nasz model?

Teraz można ocenić wydajność i skalowalność naszej pierwszej wersji. Dla każdego identyfikowanego wcześniej żądania mierzy swoje opóźnienie i liczbę jednostek żądań, których używa. Ta wartość jest wykonywana względem fikcyjnego zestawu danych zawierającego 100 000 użytkowników z 5 do 50 wpisów na użytkownika oraz do 25 komentarzy i 100 polubień na wpis.

### <a name="c1-createedit-a-user"></a>C1 Tworzenie/edytowanie użytkownika

To żądanie jest proste do wdrożenia podczas tworzenia lub aktualizowania elementu w `users` kontenerze. Żądania będą dobrze rozłożyć na wszystkie partycje, dzięki czemu `id` klucz partycji.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 7 MS | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>Pierwszym Pobierz użytkownika

Pobieranie użytkownika odbywa się przez odczytanie odpowiedniego elementu z `users` kontenera.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>C2 Utwórz/Edytuj wpis

Podobnie jak w przypadku **[C1]**, chcemy zapisywać do `posts` kontenera.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 9 MS | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>Q2 Pobieranie wpisu

Rozpoczynamy od pobrania odpowiedniego dokumentu z `posts` kontenera. Ale nie jest to wystarczające, zgodnie z naszymi specyfikacjami, należy również agregować nazwę użytkownika autora i liczbę komentarzy oraz liczbę polubień tego wpisu, które wymagają 3 dodatkowych zapytań SQL do wystawienia.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Każdy z dodatkowych zapytań filtruje klucz partycji odpowiedniego kontenera, co dokładnie ma na celu zmaksymalizowanie wydajności i skalowalności. Jednak chcemy wykonać cztery operacje, aby zwrócić pojedynczy wpis, więc poprawimy to w następnej iteracji.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 9 MS | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>Kwartał Wyświetlanie listy wpisów użytkownika w postaci krótkiej

Najpierw musimy pobrać żądane wpisy z użyciem zapytania SQL pobierającego wpisy odpowiadające temu użytkownikowi. Jednak musimy również wydać dodatkowe zapytania, aby agregować nazwę użytkownika autora oraz liczbę komentarzy i polubień.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Ta implementacja przedstawia wiele wad:

- zapytania agregujące liczby komentarzy i polubień muszą być wystawione dla każdego wpisu zwróconego przez pierwsze zapytanie,
- główne zapytanie nie odfiltruje klucza partycji `posts` kontenera, co prowadzi do skanowania w poziomie wentylatorów i partycji w ramach kontenera.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 130 MS | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>C3 Utwórz komentarz

Komentarz jest tworzony przez zapisanie odpowiedniego elementu w `posts` kontenerze.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 7 MS | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Kwartale Wyświetlanie listy komentarzy dotyczących wpisów

Zaczynamy od zapytania, które pobiera wszystkie komentarze dla tego wpisu, i ponownie musimy agregować nazwy użytkowników osobno dla każdego komentarza.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Mimo że zapytanie główne wykonuje filtrowanie według klucza partycji kontenera, agregowanie nazw użytkowników jest karane za ogólną wydajność. Poprawimy to później.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 23 MS | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>C4 Jak wpis

Podobnie jak w przypadku **[C3]**, tworzymy odpowiadający element w `posts` kontenerze.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 6 MS | 7,05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Wyświetlanie polubień wpisu

Podobnie jak w przypadku **[4 kwartale]**, będziemy wysyłać zapytania dotyczące polubień dla tego wpisu, a następnie agregować ich nazwy użytkowników.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 59 MS | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista x najnowszych wpisów utworzonych w formie krótkiej (Źródło danych)

Pobieramy najnowsze wpisy, wykonując zapytania dotyczące `posts` kontenera posortowanego według malejącej daty utworzenia, a następnie agregowania nazw użytkowników i liczby komentarzy oraz polubień dla każdego z nich.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Po ponownym uruchomieniu zapytanie początkowe nie odfiltruje klucza partycji `posts` kontenera, co wyzwala kosztowny wentylator. Ta wartość jest jeszcze gorsza, ponieważ docelowo znacznie większy zestaw wyników i posortujesz wyniki z `ORDER BY` klauzulą, co sprawia, że jest to droższe względem jednostek żądania.

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 306 MS | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Odzwierciedlanie wydajności wersji 1

Analizując problemy z wydajnością w poprzedniej sekcji, możemy identyfikować dwie główne klasy problemów:

- niektóre żądania wymagają wystawienia wielu zapytań w celu zebrania wszystkich danych, które muszą zostać zwrócone,
- Niektóre zapytania nie filtrują klucza partycji docelowych kontenerów, co prowadzi do wentylatorów, które utrudniają naszą skalowalność.

Rozwiążmy wszystkie te problemy, rozpoczynając od pierwszej.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: wprowadzenie denormalizacji w celu zoptymalizowania zapytań odczytu

Powód, dla którego musimy wydać dodatkowe żądania w niektórych przypadkach wynika z faktu, że wyniki żądania wstępnego nie zawierają wszystkich danych, które muszą zostać zwrócone. Podczas pracy z nierelacyjnym magazynem danych, takim jak Azure Cosmos DB, ten rodzaj problemu jest często rozwiązywany przez denormalizację danych w naszym zestawie danych.

W naszym przykładzie modyfikujemy elementy post, aby dodać nazwę użytkownika autora wpisu, liczbę komentarzy i liczbę polubień:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Modyfikujemy również komentarz i podobne elementy, aby dodać nazwę użytkownika, który je utworzył:

```json
{
    "id": "<comment-id>",
    "type": "comment",
    "postId": "<post-id>",
    "userId": "<comment-author-id>",
    "userUsername": "<comment-author-username>",
    "content": "<comment-content>",
    "creationDate": "<comment-creation-date>"
}

{
    "id": "<like-id>",
    "type": "like",
    "postId": "<post-id>",
    "userId": "<liker-id>",
    "userUsername": "<liker-username>",
    "creationDate": "<like-creation-date>"
}
```

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizacja komentarza i liczby takich jak Count

To, co chcemy osiągnąć, to przy każdym dodawaniu komentarza lub podobnej wartości `commentCount` `likeCount` . Gdy `posts` kontener jest partycjonowany przez `postId` , nowy element (komentarz lub podobny) i odpowiadający mu wpis znajduje się w tej samej partycji logicznej. W związku z tym można użyć [procedury składowanej](stored-procedures-triggers-udfs.md) do wykonania tej operacji.

Teraz podczas tworzenia komentarza (**[C3]**) zamiast tylko dodawania nowego elementu w `posts` kontenerze wywoływana została następująca procedura składowana w tym kontenerze:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Ta procedura składowana przyjmuje identyfikator wpisu i treść nowego komentarza jako parametry, a następnie:

- Pobiera wpis
- zwiększa wartość `commentCount`
- zastępuje wpis
- dodaje nowy komentarz

Ponieważ procedury składowane są wykonywane jako transakcje niepodzielne, gwarantowane jest, że wartość `commentCount` i rzeczywista liczba komentarzy będą zawsze zsynchronizowane.

Oczywiście wywołujemy podobną procedurę przechowywaną podczas dodawania nowych polubień, aby zwiększyć `likeCount` .

### <a name="denormalizing-usernames"></a>Denormalizacja nazw użytkowników

Nazwy użytkowników wymagają innego podejścia, ponieważ użytkownicy nie tylko znajdują się w różnych partycjach, ale w innym kontenerze. Gdy musimy deznormalizować dane między partycjami i kontenerami, możemy użyć [kanału informacyjnego zmiany](change-feed.md)kontenera źródłowego.

W naszym przykładzie używamy kanału informacyjnego zmiany `users` kontenera do reagowania, gdy użytkownicy zaktualizują swoje nazwy użytkownika. W takim przypadku propagowanie zmiany przez wywołanie innej procedury składowanej w `posts` kontenerze:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Ta procedura składowana Pobiera identyfikator użytkownika i nową nazwę użytkownika jako parametry, a następnie:

- Pobiera wszystkie elementy pasujące do `userId` (które mogą być ogłoszeniami, komentarzami lub polubieniem)
- dla każdego z tych elementów
  - zastępuje `userUsername`
  - zamienia element

> [!IMPORTANT]
> Ta operacja jest kosztowna, ponieważ wymaga wykonania tej procedury składowanej na każdej partycji `posts` kontenera. Przyjęto założenie, że większość użytkowników wybierze odpowiednią nazwę użytkownika podczas rejestracji i nie zmieni się, więc ta aktualizacja będzie działać bardzo rzadko.

## <a name="what-are-the-performance-gains-of-v2"></a>Jakie są zyski z wydajności w wersji 2?

### <a name="q2-retrieve-a-post"></a>Q2 Pobieranie wpisu

Teraz, gdy nasza została normalizacja, musimy tylko pobrać pojedynczy element, aby obsłużyć to żądanie.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 2 MS | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>Kwartale Wyświetlanie listy komentarzy dotyczących wpisów

W tym miejscu możemy wykonać zapasowe dodatkowe żądania, które pomogły pobrać nazwy użytkowników i zakończyć działanie za pomocą pojedynczego zapytania, które filtruje klucz partycji.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 4 MS | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Wyświetlanie polubień wpisu

Dokładna sytuacja podczas wyświetlania listy polubień.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 4 MS | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Sprawdzanie, czy wszystkie żądania są skalowalne

Analizując nasze Ogólne ulepszenia wydajności, nadal istnieją dwa żądania, które nie zostały w pełni zoptymalizowane: **[Q3]** i **[Q6]**. Są to żądania dotyczące zapytań, które nie filtrują w kluczu partycji docelowych kontenerów.

### <a name="q3-list-a-users-posts-in-short-form"></a>Kwartał Wyświetlanie listy wpisów użytkownika w postaci krótkiej

To żądanie już korzysta z ulepszeń wprowadzonych w wersji 2, które zapasowe dodatkowych zapytań.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Mimo że pozostałe zapytanie nie jest filtrowane w kluczu partycji `posts` kontenera.

Sposób, w jaki można myśleć o tej sytuacji, jest w rzeczywistości prosty:

1. To żądanie *musi* odfiltrować się, `userId` ponieważ chcemy pobrać wszystkie wpisy dla określonego użytkownika
1. Nie działa prawidłowo, ponieważ jest wykonywane względem `posts` kontenera, który nie jest partycjonowany przez `userId`
1. Mówiąc oczywisty, możemy rozwiązać problem z wydajnością, wykonując to żądanie w odniesieniu do kontenera, który *jest* partycjonowany przez `userId`
1. Spowoduje to wymuszenie takiego kontenera: `users` Container!

Dlatego wprowadzamy drugi poziom denormalizacji przez duplikowanie całych wpisów do `users` kontenera. Dzięki temu będziemy efektywnie uzyskać kopię naszych wpisów, które są podzielone na partycje w różnych wymiarach, co sprawia, że są one wydajniejsze do pobrania `userId` .

`users`Kontener zawiera teraz 2 rodzaje elementów:

```json
{
    "id": "<user-id>",
    "type": "user",
    "userId": "<user-id>",
    "username": "<username>"
}

{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Należy pamiętać, że:

- `type`w elemencie User wprowadzono pole umożliwiające odróżnienie użytkowników od wpisów,
- Dodaliśmy także `userId` pole do elementu użytkownika, które jest nadmiarowe z polem, `id` ale jest wymagane, ponieważ `users` kontener jest teraz podzielony przez `userId` (i nie `id` jak wcześniej)

W celu osiągnięcia tej denormalizacji ponownie użyjemy źródła zmian. Tym razem będziemy reagować na kanał informacyjny zmiany `posts` kontenera w celu wysłania nowych lub zaktualizowanych wpisów do `users` kontenera. Ponieważ listy ogłoszeń nie wymagają zwrócenia pełnej zawartości, można je obciąć w procesie.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Teraz można kierować zapytania do `users` kontenera, filtrując klucz partycji kontenera.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 4 MS | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista x najnowszych wpisów utworzonych w formie krótkiej (Źródło danych)

Firma Microsoft musi obsłużyć podobną sytuację tutaj: nawet po podzieleniu dodatkowych zapytań pozostawionych niepotrzebnie przez denormalizację wprowadzonej w wersji 2, pozostałe zapytanie nie filtruje klucza partycji kontenera:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Postępując zgodnie z tym samym podejściem, Maksymalizacja wydajności i skalowalności tego żądania wymaga, aby trafili tylko jedną partycję. Jest to konieczne, ponieważ należy zwrócić tylko ograniczoną liczbę elementów. Aby wypełnić naszą stronę główną platformy do obsługi blogów, wystarczy uzyskać 100 najnowszych wpisów, bez konieczności umieszczania ich w całym zestawie danych.

Aby zoptymalizować to ostatnie żądanie, wprowadzimy trzeci kontener do naszego projektu, całkowicie dedykowany do obsługi tego żądania. Denormalizuje nasze wpisy do nowego `feed` kontenera:

```json
{
    "id": "<post-id>",
    "type": "post",
    "postId": "<post-id>",
    "userId": "<post-author-id>",
    "userUsername": "<post-author-username>",
    "title": "<post-title>",
    "content": "<post-content>",
    "commentCount": <count-of-comments>,
    "likeCount": <count-of-likes>,
    "creationDate": "<post-creation-date>"
}
```

Ten kontener jest podzielony na partycje `type` , które zawsze będą znajdować się `post` w naszych elementach. Pozwoli to zagwarantować, że wszystkie elementy w tym kontenerze będą się w tej samej partycji.

Aby osiągnąć denormalizację, trzeba tylko podpiąć do potoku źródła zmian, który został wcześniej przesłany w celu wysłania wpisów do nowego kontenera. Ważną kwestią jest to, że musimy mieć pewność, że przechowujemy tylko 100 najnowszych wpisów; w przeciwnym razie zawartość kontenera może być większa niż maksymalny rozmiar partycji. Jest to realizowane przez wywołanie [wyzwalacza po](stored-procedures-triggers-udfs.md#triggers) każdym dodaniu dokumentu do kontenera:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

Oto treść wyzwalacza końcowego, który obcina kolekcję:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Ostatnim krokiem jest przekierowanie zapytania do naszego nowego `feed` kontenera:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Pisanie pojedynczego elementu w kontenerze Użytkownicy" border="false":::

| **Opóźnienie** | **Opłata za RU** | **Wydajność** |
| --- | --- | --- |
| 9 MS | 16,97 RU | ✅ |

## <a name="conclusion"></a>Podsumowanie

Przyjrzyjmy się ogólnym ulepszonym funkcjom dotyczącym wydajności i skalowalności w różnych wersjach tego projektu.

| | Wersja 1 | Wersja 2 | Czytanie |
| --- | --- | --- | --- |
| **C1** | 7 MS/5,71 RU | 7 MS/5,71 RU | 7 MS/5,71 RU |
| **Pierwszym** | 2 MS/1 RU | 2 MS/1 RU | 2 MS/1 RU |
| **C2** | 9 MS/8,76 RU | 9 MS/8,76 RU | 9 MS/8,76 RU |
| **Q2** | 9 MS/19,54 RU | 2 MS/1 RU | 2 MS/1 RU |
| **Kwartał** | 130 MS/619,41 RU | 28 MS/201,54 RU | 4 MS/6,46 RU |
| **C3** | 7 MS/8,57 RU | 7 MS/15,27 RU | 7 MS/15,27 RU |
| **Kwartale** | 23 MS/27,72 RU | 4 MS/7,72 RU | 4 MS/7,72 RU |
| **C4** | 6 MS/7,05 RU | 7 MS/14,67 RU | 7 MS/14,67 RU |
| **[Q5]** | 59 MS/58,92 RU | 4 MS/8,92 RU | 4 MS/8,92 RU |
| **[Q6]** | 306 MS/2063,54 RU | 83 MS/532,33 RU | 9 MS/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Zoptymalizowany jest scenariusz z przeczytaniem i dużym obciążeniem

Być może zauważono, że firma Microsoft dąży do osiągnięcia poprawy wydajności żądań odczytu (zapytań) przy kosztach żądań zapisu (polecenia). W wielu przypadkach operacje zapisu teraz wyzwalają kolejną denormalizację za poorednictwem źródeł zmian, co sprawia, że są one bardziej kosztowne i nie zmaterializowania.

Jest to uzasadnione faktem, że platforma do obsługi blogów (podobnie jak większość aplikacji społecznościowych) jest w dużym stopniu odczytywana, co oznacza, że ilość żądań odczytu, które mają być objęte, jest zazwyczaj kolejnością o wielkości większej niż liczba żądań zapisu. Dlatego warto mieć pewność, że żądania zapisu są droższe do wykonania, aby żądania odczytu były tańsze i lepsze.

Jeśli przejdziemy do najwyższej najwyższej optymalizacji, **[Q6]** przeprowadzono od 2000 + jednostek ru do zaledwie 17 jednostek ru; osiągnięto, że przez denormalizowanie wpisów na koszt około 10 jednostek ru na element. Ponieważ będziemy korzystać z większej liczby żądań kanału informacyjnego niż tworzenie lub aktualizowanie wpisów, koszt tej denormalizacji jest nieznaczny, biorąc pod uwagę ogólne oszczędności.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalizację można zastosować przyrostowo

Ulepszenia skalowalności omówione w tym artykule obejmują denormalizację i duplikowanie danych w zestawie danych. Należy zauważyć, że te optymalizacje nie muszą być wprowadzane w miejscu 1. Zapytania filtrujące klucze partycji działają lepiej na dużą skalę, ale zapytania między partycjami mogą być całkowicie akceptowalne, jeśli są wywoływane rzadko lub z ograniczonym zestawem danych. Jeśli właśnie tworzysz prototyp lub uruchamiasz produkt z niewielką i kontrolowaną bazą użytkowników, możesz zastanowić się nad tymi ulepszeniami w przyszłości. Ważne jest, aby [monitorować](use-metrics.md) wydajność modelu, aby można było określić, czy i kiedy czas ma być przydany do nich.

Kanał informacyjny zmiany używany do dystrybuowania aktualizacji do innych kontenerów trwale przechowuje wszystkie aktualizacje. Dzięki temu można żądać wszystkich aktualizacji od momentu utworzenia kontenera i nieznormalizowanych widoków jako jednorazowej operacji przechwytywania, nawet jeśli system ma już wiele danych.

## <a name="next-steps"></a>Następne kroki

Po tym wprowadzeniu do praktycznego modelowania i partycjonowania danych warto zapoznać się z następującymi artykułami, aby przejrzeć omówione koncepcje:

- [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md)
- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Źródło zmian w Azure Cosmos DB](change-feed.md)
