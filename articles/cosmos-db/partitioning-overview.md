---
title: Partycjonowanie w usłudze Azure Cosmos DB
description: Więcej informacji na temat partycjonowania Azure Cosmos DB, najlepszych rozwiązań w przypadku wybierania klucza partycji oraz zarządzania partycjami logicznymi
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481837"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partycjonowanie w usłudze Azure Cosmos DB

Azure Cosmos DB używa partycjonowania do skalowania poszczególnych kontenerów w bazie danych w celu spełnienia wymagań dotyczących wydajności aplikacji. W przypadku partycjonowania elementy w kontenerze są podzielone na odrębne podzestawy o nazwie *partycje logiczne*. Partycje logiczne są tworzone na podstawie wartości *klucza partycji* , który jest skojarzony z każdym elementem w kontenerze. Wszystkie elementy w partycji logicznej mają tę samą wartość klucza partycji.

Na przykład kontener zawiera elementy. Każdy element ma unikatową wartość `UserID` właściwości. Jeśli `UserID` program służy jako klucz partycji dla elementów w kontenerze, a istnieją 1 000 wartości unikatowych `UserID` , dla kontenera są tworzone partycje logiczne 1 000.

Oprócz klucza partycji, który określa partycję logiczną elementu, każdy element w kontenerze ma *Identyfikator elementu* (unikatowy w obrębie partycji logicznej). Połączenie klucza partycji i *identyfikatora elementu* tworzy *indeks*elementu, który jednoznacznie identyfikuje element.

[Wybór klucza partycji](partitioning-overview.md#choose-partitionkey) to ważna decyzja, która będzie miała wpływ na wydajność aplikacji.

## <a name="managing-logical-partitions"></a>Zarządzanie partycjami logicznymi

Azure Cosmos DB w sposób przezroczysty i automatycznie zarządza umieszczaniem partycji logicznych w partycjach fizycznych, aby skutecznie spełnić wymagania dotyczące skalowalności i wydajności kontenera. W miarę wzrostu wymagań dotyczących przepływności i magazynowania aplikacji Azure Cosmos DB przenosi partycje logiczne, aby automatycznie rozłożyć obciążenie na większą liczbę partycji fizycznych. Więcej informacji o [partycjach fizycznych](partition-data.md#physical-partitions)można znaleźć w części.

Azure Cosmos DB używa partycjonowania opartego na skrócie, aby rozłożyć partycje logiczne między partycjami fizycznymi. Azure Cosmos DB skrót wartości klucza partycji elementu. Wynik zmieszany Określa partycję fizyczną. Następnie Azure Cosmos DB przydzieli kluczowe miejsce skrótów kluczy partycji równomiernie między partycjami fizycznymi.

Transakcje (w procedurach składowanych lub wyzwalaczach) są dozwolone tylko dla elementów w pojedynczej partycji logicznej.

Aby dowiedzieć się więcej o tym, [jak Azure Cosmos DB zarządza partycjami](partition-data.md). (Nie trzeba zrozumieć wewnętrznych szczegółów, aby kompilować lub uruchamiać aplikacje, ale dodano je do czytnika chcesz wiedzieć).

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Wybieranie klucza partycji

Klucz partycji ma dwa składniki: **ścieżkę klucza partycji** i **wartość klucza partycji**. Rozważmy na przykład element {"userId": "Andrew", "worksFor": "Microsoft"} Jeśli wybierzesz "userId" jako klucz partycji, poniżej przedstawiono dwa składniki kluczy partycji:

* Ścieżka klucza partycji (na przykład: "/userId"). Ścieżka klucza partycji akceptuje znaki alfanumeryczne i podkreślenia (_). Można również użyć zagnieżdżonych obiektów przy użyciu notacji ścieżki standardowej (/).

* Wartość klucza partycji (na przykład: "Andrew"). Wartość klucza partycji może być typu String lub numeric.

Aby dowiedzieć się więcej na temat limitów przepływności, magazynu i długości klucza partycji, zobacz artykuł dotyczący [przydziałów usługi Azure Cosmos DB](concepts-limits.md) .

Wybór klucza partycji to prosty, ale istotny wybór w Azure Cosmos DB. Po wybraniu klucza partycji nie można go zmienić w miejscu. Jeśli musisz zmienić klucz partycji, Przenieś dane do nowego kontenera przy użyciu nowego żądanego klucza partycji.

W przypadku **wszystkich** kontenerów klucz partycji powinien:

* Należy do właściwości, która ma wartość, która nie jest zmieniana. Jeśli właściwość jest kluczem partycji, nie można zaktualizować wartości tej właściwości.
* Ma wysoką kardynalność. Innymi słowy właściwość powinna mieć szeroką gamę możliwych wartości.
* Rozproszone użycie jednostek żądań (RU) i magazyn danych przez wszystkie partycje logiczne. Zapewnia to nawet użycie RU i dystrybucję magazynu dla partycji fizycznych.

Jeśli potrzebujesz [wieloelementowych transakcji kwasowych](database-transactions-optimistic-concurrency.md#multi-item-transactions) w Azure Cosmos DB, musisz użyć [procedur składowanych i wyzwalaczy](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Wszystkie procedury składowane oparte na języku JavaScript i wyzwalacze są objęte zakresem pojedynczej partycji logicznej.

## <a name="partition-keys-for-read-heavy-containers"></a>Klucze partycji dla kontenerów z dużą ilością odczytu

W przypadku większości kontenerów powyższe kryteria należy wziąć pod uwagę podczas wybierania klucza partycji. W przypadku dużych kontenerów z możliwością odczytu można jednak wybrać klucz partycji, który często pojawia się jako filtr w zapytaniach. Zapytania mogą być [efektywnie kierowane tylko do odpowiednich partycji fizycznych](how-to-query-container.md#in-partition-query) poprzez dołączenie klucza partycji do predykatu filtru.

Jeśli większość żądań obciążeń jest kwerendami i większość zapytań ma filtr równości dla tej samej właściwości, ta właściwość może być dobrym kluczem partycji. Na przykład, jeśli często uruchamiasz zapytanie, które jest filtrowane `UserID` , a następnie wybranie `UserID` klucza partycji zmniejsza liczbę [zapytań między partycjami](how-to-query-container.md#avoiding-cross-partition-queries).

Jeśli jednak kontener jest mały, prawdopodobnie nie masz wystarczającej liczby partycji fizycznych, aby nie trzeba było zajmować się wpływem na wydajność zapytań między partycjami. Większość małych kontenerów w Azure Cosmos DB wymaga tylko jednej lub dwóch partycji fizycznych.

Jeśli kontener może się zwiększać do więcej niż kilku partycji fizycznych, należy upewnić się, że wybierasz klucz partycji, który minimalizuje zapytania między partycjami. Kontener będzie wymagał więcej niż kilku partycji fizycznych, jeśli spełniony jest jeden z następujących warunków:

* Kontener będzie miał ponad 30 000 RU
* Kontener będzie przechowywać ponad 100 GB danych

## <a name="using-item-id-as-the-partition-key"></a>Używanie identyfikatora elementu jako klucza partycji

Jeśli kontener ma właściwość, która ma szeroką gamę możliwych wartości, prawdopodobnie jest wybór doskonałej partycji. Jednym z możliwych przykładowych właściwości jest *Identyfikator elementu*. W przypadku małych kontenerów z możliwością odczytu i dużych kontenerów o dowolnym rozmiarze *Identyfikator elementu* jest naturalnie doskonałym wyborem dla klucza partycji.

*Identyfikator elementu* właściwości systemu jest gwarantowany dla każdego elementu w kontenerze Cosmos. Mogą istnieć inne właściwości reprezentujące identyfikator logiczny elementu. W wielu przypadkach są one również doskonałymi opcjami kluczy partycji z tego samego powodu, co *Identyfikator elementu*.

*Identyfikator elementu* to doskonały wybór klucza partycji z następujących powodów:

* Istnieje szeroki zakres możliwych wartości (jeden unikatowy *Identyfikator elementu* dla każdego elementu).
* Ponieważ istnieje unikatowy *Identyfikator elementu* dla każdego elementu, *Identyfikator elementu* wykonuje doskonałe zadanie w przypadku równomiernego zrównoważenia zużycia ru i magazynu danych.
* Możesz łatwo wykonywać efektywne odczyty punktów, ponieważ zawsze znasz klucz partycji elementu, jeśli znasz jego *Identyfikator elementu*.

Niektóre kwestie, które należy wziąć pod uwagę podczas wybierania *identyfikatora elementu* jako klucza partycji, obejmują:

* Jeśli *Identyfikator elementu* jest kluczem partycji, będzie on unikatowym identyfikatorem w całym kontenerze. Nie będzie możliwe posiadanie elementów mających zduplikowany *Identyfikator elementu*.
* Jeśli masz kontener do odczytu, który ma wiele [partycji fizycznych](partition-data.md#physical-partitions), zapytania będą bardziej wydajne, jeśli mają filtr równości z *identyfikatorem elementu*.
* Nie można uruchomić procedur składowanych ani wyzwalaczy na wielu partycjach logicznych.

## <a name="next-steps"></a>Następne kroki

* Informacje o [partycjonowaniu i skalowaniu w poziomie Azure Cosmos DB](partition-data.md).
* Informacje o [aprowizacji przepływności w Azure Cosmos DB](request-units.md).
* Informacje o [dystrybucji globalnej w Azure Cosmos DB](distribute-data-globally.md).
