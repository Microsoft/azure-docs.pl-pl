---
title: Współpraca z interfejsem API zintegrowanej kwerendy języka JavaScript w Azure Cosmos DB procedury składowane i wyzwalacze
description: W tym artykule przedstawiono koncepcje dotyczące interfejsu API zapytań zintegrowanych z językiem JavaScript do tworzenia procedur składowanych i wyzwalaczy w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340978"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Interfejs API zapytań JavaScript w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Oprócz wydawania zapytań przy użyciu interfejsu API SQL w Azure Cosmos DB, [zestaw SDK po stronie serwera Cosmos DB](https://github.com/Azure/azure-cosmosdb-js-server/) udostępnia interfejs JavaScript do wykonywania zoptymalizowanych zapytań w Cosmos DB procedurach składowanych i wyzwalaczach. Nie trzeba znać języka SQL, aby używać tego interfejsu JavaScript. Interfejs API zapytań języka JavaScript umożliwia Programistyczne tworzenie zapytań przez przekazywanie funkcji predykatów do sekwencji wywołań funkcji, z składnią ECMAScript5's wbudowane i popularne biblioteki języka JavaScript, takie jak Lodash. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript i efektywnie wykonywane przy użyciu Azure Cosmos DB indeksów.

## <a name="supported-javascript-functions"></a>Obsługiwane funkcje języka JavaScript

| **Funkcja** | **Opis** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Uruchamia wywołanie łańcuchowe, które musi zostać zakończone wartością ().|
|`filter(predicateFunction [, options] [, callback])`|Filtruje dane wejściowe przy użyciu funkcji predykatu zwracającej wartość true/false w celu przefiltrowania/wyewidencjonowania dokumentów wejściowych do wynikowego zestawu. Ta funkcja zachowuje się podobnie do klauzuli WHERE w SQL.|
|`flatten([isShallow] [, options] [, callback])`|Łączy i spłaszcza tablice z każdego elementu wejściowego w jedną tablicę. Ta funkcja zachowuje się podobnie do SelectMany w LINQ.|
|`map(transformationFunction [, options] [, callback])`|Stosuje projekcję przy użyciu funkcji transformacji, która mapuje każdy element wejściowy do obiektu lub wartości JavaScript. Ta funkcja zachowuje się podobnie do klauzuli SELECT w SQL.|
|`pluck([propertyName] [, options] [, callback])`|Ta funkcja jest skrótem dla mapy, która wyodrębnia wartość pojedynczej właściwości z każdego elementu wejściowego.|
|`sortBy([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumenty w strumieniu dokumentu wejściowego w kolejności rosnącej przy użyciu danego predykatu. Ta funkcja zachowuje się podobnie do klauzuli ORDER BY w SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Tworzy nowy zestaw dokumentów, sortując dokumenty w strumieniu dokumentu wejściowego w kolejności malejącej przy użyciu podanego predykatu. Ta funkcja zachowuje się podobnie do klauzuli ORDER BY x DESC w SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Wykonuje samosprzężenie z tablicą wewnętrzną i dodaje wyniki z obu stron jako krotki do rzutowania wyników. Na przykład przyłączenie do dokumentu osoby. zwierzęta domowe spowodują utworzenie krotek [Person, PET]. Jest to podobne do SelectMany w ŁĄCZu .NET.|

Po dołączeniu wewnątrz funkcji predykatu i/lub selektora następujące konstrukcje JavaScript są automatycznie zoptymalizowane pod kątem uruchamiania bezpośrednio na Azure Cosmos DB indeksy:

- Operatory proste `=` `+` `-` `*` : `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literały, w tym literał obiektu: {}
- var, Return

Następujące konstrukcje JavaScript nie są zoptymalizowane pod kątem Azure Cosmos DB indeksów:

- Przepływ sterowania (na przykład jeśli, w przypadku, while)
- Wywołania funkcji

Aby uzyskać więcej informacji, zobacz [dokumentację języka JavaScript po stronie serwera Cosmos DB](https://github.com/Azure/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Arkusz Ściągawka SQL do JavaScript

W poniższej tabeli przedstawiono różne zapytania SQL i odpowiednie zapytania języka JavaScript. Podobnie jak w przypadku zapytań SQL, właściwości (na przykład item.id) uwzględniają wielkość liter.

> [!NOTE]
> `__` (podwójne podkreślenie) jest aliasem funkcji `getContext().getCollection()` podczas korzystania z interfejsu API zapytań języka JavaScript.

|**SQL**|**Interfejs API zapytań JavaScript**|**Opis**|
|---|---|---|
|ZAZNACZENIA<br>Z witryny docs| __. map (funkcja (doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;Zwróć dokument;<br>});|Wyniki we wszystkich dokumentach (z podziałem na strony z tokenem kontynuacji) zgodnie z oczekiwaniami.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako MSG,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>Z witryny docs|__. map (funkcja (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;przesłać<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc. Message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;akcje: doc. Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projektuje identyfikator, komunikat (alias do MSG) i akcję z wszystkich dokumentów.|
|ZAZNACZENIA<br>Z witryny docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__. Filter (funkcja (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;Return doc.id = = = "X998_Y998";<br>});|Zapytania o dokumenty z predykatem: ID = "X998_Y998".|
|ZAZNACZENIA<br>Z witryny docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Tagi, 123)|__. Filter (funkcja (x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;zwraca x. Tags && x. Tags. indexOf (123) >-1;<br>});|Zapytania o dokumenty, które mają właściwość tagów i Tagi, są tablicą zawierającą wartość 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message jako komunikat<br>Z witryny docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. ID = "X998_Y998"|__. łańcuch ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (funkcja (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Return doc.id = = = "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. map (funkcja (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;przesłać<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc. Message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>. Value ();|Wykonuje zapytania o dokumenty z predykatem, identyfikatorem "X998_Y998", a następnie projektuje identyfikator i komunikat (alias do MSG).|
|Wybierz tag wartości<br>Z witryny docs<br>Tag JOIN w witrynie docs. Tabliczk<br>PORZĄDKUj według docs._ts|__. łańcuch ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (funkcja (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Wróć do dokumentu. Tagi && Array. IsArray (doc. Tagi);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. SortBy — (funkcja (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zwróć doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. oskubane ("Tagi")<br>&nbsp;&nbsp;&nbsp;&nbsp;. Spłaszcz ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Value ()|Filtruje dokumenty, które mają właściwość Array, Tagi i sortuje dokumenty uzyskane według właściwości systemowej sygnatury czasowej _ts, a następnie projekty i spłaszczają tablicę tagów.|

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o pojęciach i sposobie pisania i używania procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w programie Azure Cosmos DB:

- [Jak napisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript](how-to-write-javascript-query-api.md)
- [Praca z Azure Cosmos DB procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika](stored-procedures-triggers-udfs.md)
- [Jak używać procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Dokumentacja interfejsu API po stronie serwera Azure Cosmos DB JavaScript](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
