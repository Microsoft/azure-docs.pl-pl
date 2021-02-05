---
title: Pracuj z procedurami składowanymi, wyzwalaczami i UDF w Azure Cosmos DB
description: W tym artykule przedstawiono pojęcia, takie jak procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w programie Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: ad9e6b99b396465c2cff95bd6ab340ef9d668085
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575961"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Usługa Azure Cosmos DB zapewnia zintegrowane z językiem, transakcyjne wykonywanie kodu języka JavaScript. W przypadku korzystania z interfejsu API SQL w Azure Cosmos DB można napisać **procedury składowane**, **wyzwalacze** i **funkcje zdefiniowane przez użytkownika (UDF)** w języku JavaScript. Logikę można napisać w języku JavaScript i wykonać ją w aparacie bazy danych. Można tworzyć i wykonywać wyzwalacze, procedury składowane i UDF przy użyciu [Azure Portal](https://portal.azure.com/), [zintegrowanego interfejsu API języka JavaScript w Azure Cosmos DB](javascript-query-api.md) lub [Cosmos DB zestaw SDK klienta interfejsu API SQL](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Zalety korzystania z programowania po stronie serwera

Pisanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) w języku JavaScript umożliwia tworzenie rozbudowanych aplikacji i zapewnia następujące korzyści:

* **Logika proceduralna:** JavaScript jako język programowania wysokiego poziomu, który zapewnia bogaty i przyjazny interfejs do wyrażania logiki biznesowej. Można wykonać sekwencję złożonych operacji na danych.

* **Transakcje niepodzielne:** Operacje bazy danych Azure Cosmos DB, które są wykonywane w ramach jednej procedury składowanej lub wyzwalacza, są niepodzielne. Ta funkcja niepodzielna umożliwia aplikacji łączenie powiązanych operacji w pojedynczą partię, dzięki czemu wszystkie operacje kończą się powodzeniem lub żadna z nich nie powiedzie się.

* **Wydajność:** Dane JSON są wewnętrznie mapowane na system typów języka JavaScript. To mapowanie pozwala na wiele optymalizacji, takich jak materializację z opóźnieniem dokumentów JSON w puli buforów i udostępnienie ich na żądanie do wykonywanego kodu. Istnieją inne korzyści dotyczące wydajności związane z dostarczaniem logiki biznesowej do bazy danych programu, w tym:

   * Tworzenie *partii:* Można grupować operacje, takie jak wstawiane i przesyłane zbiorczo. Koszty opóźnienia ruchu sieciowego i nakładu pracy magazynu w celu utworzenia oddzielnych transakcji są znacznie ograniczone.

   * *Kompilacja wstępna:* Procedury składowane, wyzwalacze i UDF są niejawnie wstępnie skompilowane w formacie kodu bajtowego w celu uniknięcia kosztów kompilacji w czasie każdego wywołania skryptu. Ze względu na wstępną kompilację, wywołanie procedur składowanych jest szybkie i ma niską wartość.

   * *Sekwencjonowanie:* Czasami Operacje wymagają mechanizmu wyzwalania, który może wykonać jedną lub więcej aktualizacji danych. Oprócz niepodzielenia można także skorzystać z wydajności po wykonaniu tej operacji po stronie serwera.

* **Hermetyzacja:** Procedury składowane mogą służyć do grupowania logiki w jednym miejscu. Hermetyzacja dodaje warstwę abstrakcji na danych, co pozwala na rozdzielenie aplikacji niezależnie od danych. Ta warstwa abstrakcji jest przydatna, gdy dane są mniej schematowe i nie trzeba zarządzać dodawaniem dodatkowych logiki bezpośrednio do aplikacji. Streszczenie umożliwia zachowanie bezpieczeństwa danych przez usprawnienie dostępu ze skryptów.

> [!TIP]
> Procedury składowane najlepiej nadają się do operacji zapisujących duże i wymagają transakcji w ramach wartości klucza partycji. Przy podejmowaniu decyzji o tym, czy należy używać procedur składowanych, Zoptymalizuj około hermetyzowania maksymalnej liczby możliwych zapisów. Ogólnie mówiąc, procedury składowane nie są najbardziej wydajnymi sposobami wykonywania dużej liczby operacji odczytu lub zapytań, dlatego przy użyciu procedur składowanych w celu przeprowadzenia wsadowej dużej liczby odczytów w celu zwrócenia ich klientowi nie zostanie nadana odpowiednia korzyść. W celu uzyskania najlepszej wydajności te operacje odczytujące duże informacje powinny być wykonywane po stronie klienta przy użyciu zestawu SDK Cosmos. 

## <a name="transactions"></a>Transakcje

Transakcja w typowej bazie danych może być zdefiniowana jako sekwencja operacji wykonywanych jako pojedyncza jednostka logiczna. Każda transakcja zapewnia **gwarancje własności kwasowej**. KWAS jest dobrze znanym akronimem, który **oznacza: tomicity**, **C** onsistency, **i** solation i **D** urability. 

* Niepodzielność gwarantuje, że wszystkie operacje wykonywane w ramach transakcji są traktowane jako jedna jednostka, a wszystkie z nich są zatwierdzone lub żadna z nich nie jest. 

* Spójność gwarantuje, że dane są zawsze w prawidłowym stanie między transakcjami. 

* Izolacja gwarantuje, że żadne dwie transakcje nie zakłócają pracy — wiele systemów komercyjnych zapewnia wiele poziomów izolacji, które mogą być używane w zależności od potrzeb aplikacji. 

* Trwałość gwarantuje, że wszelkie zmiany, które są zatwierdzone w bazie danych, zawsze będą obecne.

W Azure Cosmos DB środowisko uruchomieniowe JavaScript jest hostowane w aparacie bazy danych. W związku z tym żądania wykonywane w ramach procedur składowanych i wyzwalaczy są wykonywane w tym samym zakresie co sesja bazy danych. Ta funkcja umożliwia Azure Cosmos DB zagwarantowanie właściwości kwasu dla wszystkich operacji, które są częścią procedury składowanej lub wyzwalacza. Aby zapoznać się z przykładami, zobacz artykuł [jak zaimplementować transakcje](how-to-write-stored-procedures-triggers-udfs.md#transactions) .

### <a name="scope-of-a-transaction"></a>Zakres transakcji

Procedury składowane są skojarzone z kontenerem usługi Azure Cosmos, a wykonywanie procedury składowanej jest ograniczone do klucza partycji logicznej. Procedury składowane muszą zawierać wartość klucza partycji logicznej podczas wykonywania, która definiuje partycję logiczną dla zakresu transakcji. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [partycjonowania Azure Cosmos DB](partitioning-overview.md) .

### <a name="commit-and-rollback"></a>Zatwierdź i Wycofaj

Transakcje są natywnie zintegrowane z Azure Cosmos DB modelem programowania JavaScript. W ramach funkcji języka JavaScript wszystkie operacje są automatycznie zawijane w ramach jednej transakcji. Jeśli logika JavaScript w procedurze składowanej zakończy się bez żadnych wyjątków, wszystkie operacje w ramach transakcji zostaną zatwierdzone do bazy danych. Instrukcje, takie jak `BEGIN TRANSACTION` i `COMMIT TRANSACTION` (znane jako relacyjne bazy danych), są niejawne w Azure Cosmos DB. W przypadku wystąpienia dowolnego wyjątku ze skryptu środowisko uruchomieniowe JavaScript Azure Cosmos DB będzie wycofać całą transakcję. W związku z tym zgłaszanie wyjątku jest skutecznym odpowiednikiem `ROLLBACK TRANSACTION` w Azure Cosmos DB.

### <a name="data-consistency"></a>Spójność danych

Procedury składowane i wyzwalacze są zawsze wykonywane dla repliki podstawowej kontenera usługi Azure Cosmos. Ta funkcja zapewnia, że odczyty z procedur składowanych zapewniają [silną spójność](./consistency-levels.md). Zapytania używające funkcji zdefiniowanych przez użytkownika mogą być wykonywane na podstawowej lub dowolnej replice pomocniczej. Procedury składowane i wyzwalacze są przeznaczone do obsługi zapisów transakcyjnych, ponieważ logika tylko do odczytu jest najlepszym zaimplementowana jako logika po stronie aplikacji i zapytania przy użyciu [zestawów SDK interfejsu API programu SQL Azure Cosmos DB](sql-api-dotnet-samples.md). 

> [!TIP]
> Zapytania wykonywane w ramach procedury składowanej lub wyzwalacza mogą nie zobaczyć zmian elementów wykonanych przez tę samą transakcję skryptu. Ta instrukcja dotyczy zarówno zapytań SQL, jak `getContent().getCollection.queryDocuments()` i zintegrowanych zapytań dotyczących języka, takich jak `getContext().getCollection().filter()` .

## <a name="bounded-execution"></a>Powiązane wykonywanie

Wszystkie operacje Azure Cosmos DB muszą zakończyć się w określonym limicie czasu. Procedury składowane mają limit czasu wynoszący 5 sekund. To ograniczenie ma zastosowanie do funkcji języka JavaScript — procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika. Jeśli operacja nie zostanie zakończona w tym limicie czasu, transakcja zostanie wycofana.

Można upewnić się, że funkcje języka JavaScript kończą się w określonym czasie, lub zaimplementować model oparty na kontynuacji, aby wykonać zadanie wsadowe/wznowienia. Aby uprościć opracowywanie procedur składowanych i wyzwalaczy w celu obsługi limitów czasu, wszystkie funkcje w ramach kontenera Azure Cosmos (na przykład tworzenie, odczytywanie, aktualizowanie i usuwanie elementów) zwracają wartość logiczną, która reprezentuje, czy ta operacja zostanie ukończona. Jeśli ta wartość jest fałszywa, oznacza to, że procedura musi zajmować wykonywanie, ponieważ skrypt zużywa więcej czasu lub zainicjowaną przepływność od skonfigurowanej wartości. Operacje znajdujące się w kolejce przed pierwszą zaakceptowaną operacją magazynu są gwarantowane, gdy procedura składowana zostanie ukończona i nie będzie kolejkować kolejnych żądań. W ten sposób operacje powinny być umieszczane w kolejce pojedynczo przy użyciu konwencji wywołania zwrotnego języka JavaScript do zarządzania przepływem sterowania skryptu. Ponieważ skrypty są wykonywane w środowisku po stronie serwera, są one ściśle regulowane. Skrypty, które wielokrotnie naruszają granice wykonywania, mogą być oznaczone jako nieaktywne i nie mogą być wykonywane i powinny być tworzone ponownie w celu uwzględnienia granic wykonania.

Funkcje języka JavaScript są również uzależnione od [przepustowości.](request-units.md) Funkcje języka JavaScript mogą potencjalnie zakończyć korzystanie z dużej liczby jednostek żądania w krótkim czasie i mogą być ograniczone, jeśli osiągnięty zostanie limit wydajności aprowizacji. Należy pamiętać, że skrypty zużywają dodatkową przepływność poza przepływność poświęcaną na wykonywanie operacji bazy danych, mimo że operacje bazy danych są nieco tańsze niż wykonywanie tych samych operacji od klienta.

## <a name="triggers"></a>Wyzwalacze

Usługa Azure Cosmos DB obsługuje dwa typy wyzwalaczy:

### <a name="pre-triggers"></a>Wyzwalacze wykonywane przed operacją

Usługa Azure Cosmos DB udostępnia wyzwalacze, które mogą być wywoływane przez wykonanie operacji w obrębie elementu usługi Azure Cosmos. Można na przykład wybrać wyzwalacz wykonywany przed operacją podczas tworzenia elementu. W takim przypadku wyzwalacz tego typu zostanie uruchomiony przed utworzeniem elementu. Wyzwalacze wykonywane przed operacją nie mogą mieć żadnych parametrów wejściowych. W razie potrzeby obiekt żądania może służyć do aktualizowania treści dokumentu z oryginalnego żądania. Podczas rejestrowania wyzwalaczy użytkownicy mogą określić operacje, z którymi można je uruchamiać. Jeśli wyzwalacz został utworzony przy użyciu elementu `TriggerOperation.Create`, oznacza to, że użycie wyzwalacza w operacji zastępowania nie będzie dozwolone. Aby zapoznać się z przykładami, zobacz artykuł [jak pisać wyzwalacze](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

### <a name="post-triggers"></a>Wyzwalacze wykonywane po operacji

Podobnie jak przed wyzwalaczami, po wyzwalaczach są również kojarzone z operacją w elemencie Cosmos platformy Azure i nie wymagają żadnych parametrów wejściowych. Są one uruchamiane *po* zakończeniu operacji i dostęp do komunikatu odpowiedzi wysyłanego do klienta. Aby zapoznać się z przykładami, zobacz artykuł [jak pisać wyzwalacze](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

> [!NOTE]
> Zarejestrowane wyzwalacze nie są uruchamiane automatycznie, gdy są wykonywane odpowiednie operacje (Tworzenie/usuwanie/zamienianie/aktualizowanie). Należy je jawnie wywoływać podczas wykonywania tych operacji. Aby dowiedzieć się więcej, zobacz artykuł [jak uruchamiać wyzwalacze](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) .

## <a name="user-defined-functions"></a><a id="udfs"></a>Funkcje zdefiniowane przez użytkownika

[Funkcje zdefiniowane przez użytkownika](sql-query-udfs.md) (UDF) służą do rozszerzonej składni języka zapytań interfejsu API SQL i w prosty sposób implementują niestandardową logikę biznesową. Mogą być wywoływane tylko w ramach zapytań. UDF nie mają dostępu do obiektu kontekstu i są przeznaczone do użycia jako tylko obliczenia JavaScript. W związku z tym UDF można uruchamiać w replikach pomocniczych. Aby zapoznać się z przykładami, zobacz artykuł [jak pisać funkcje zdefiniowane przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md#udfs) .

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>Interfejs API zapytań zintegrowanych z językiem JavaScript

Oprócz wydawania zapytań przy użyciu składni zapytań interfejsu API SQL, [zestaw SDK po stronie serwera](https://azure.github.io/azure-cosmosdb-js-server) umożliwia wykonywanie zapytań za pomocą interfejsu JavaScript bez znajomości języka SQL. Interfejs API zapytań języka JavaScript umożliwia Programistyczne tworzenie zapytań przez przekazywanie funkcji predykatu do sekwencji wywołań funkcji. Zapytania są analizowane przez środowisko uruchomieniowe JavaScript i są wykonywane efektywnie w Azure Cosmos DB. Aby dowiedzieć się więcej o obsłudze interfejsu API zapytań języka JavaScript, zobacz artykuł [Praca z interfejsem API programu Integrated Query Language języka JavaScript](javascript-query-api.md) . Aby zapoznać się z przykładami, zobacz artykuł [jak pisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript](how-to-write-javascript-query-api.md) .

## <a name="next-steps"></a>Następne kroki

Informacje o sposobach pisania i używania procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w programie Azure Cosmos DB z następującymi artykułami:

* [Pisanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md)

* [Jak używać procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika](how-to-use-stored-procedures-triggers-udfs.md)

* [Praca ze zintegrowanym interfejsem API zapytań języka JavaScript](javascript-query-api.md)