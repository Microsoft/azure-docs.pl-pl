---
title: Wykonywanie zapytań dla kontenerów w usłudze Azure Cosmos DB
description: Dowiedz się, jak wykonywać zapytania dotyczące kontenerów w Azure Cosmos DB przy użyciu zapytań w partycji i na wielu partycjach
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: e948031d3d1d03890bfcfccd65424a15e6e314cd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276115"
---
# <a name="query-an-azure-cosmos-container"></a>Wykonywanie zapytania dotyczącego kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak wykonywać zapytania dla kontenera (kolekcji, grafu lub tabeli) w usłudze Azure Cosmos DB. W szczególności obejmuje to, w jaki sposób zapytania dotyczące partycji i wielu partycji działają w Azure Cosmos DB.

## <a name="in-partition-query"></a>Zapytanie wewnątrz partycji

W przypadku wykonywania zapytań dotyczących danych z kontenerów, jeśli zapytanie ma określony filtr klucza partycji, Azure Cosmos DB automatycznie optymalizuje zapytanie. Kieruje zapytanie do [partycji fizycznych](partitioning-overview.md#physical-partitions) odpowiadających wartościom klucza partycji określonym w filtrze.

Rozważmy na przykład poniższe zapytanie z filtrem równości `DeviceId` . Jeśli to zapytanie zostanie uruchomione w kontenerze partycjonowanym na `DeviceId` , to zapytanie zostanie przefiltrowane na jedną partycję fizyczną.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Podobnie jak w przypadku wcześniejszego przykładu, to zapytanie będzie również filtrowane na pojedynczej partycji. Dodanie dodatkowego filtru nie `Location` powoduje zmiany:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Oto zapytanie, które ma filtr zakresu w kluczu partycji i nie należy do zakresu jednej partycji fizycznej. Aby można było wykonać zapytanie w ramach partycji, zapytanie musi mieć filtr równości, który zawiera klucz partycji:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Zapytanie obejmujące wiele partycji

Następujące zapytanie nie ma filtru dla klucza partycji ( `DeviceId` ). W związku z tym musi ona mieć wentylator we wszystkich partycjach fizycznych, w których są uruchamiane względem indeksu każdej partycji:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Każda partycja fizyczna ma swój własny indeks. W związku z tym podczas uruchamiania zapytania między partycjami w kontenerze efektywnie działa jedno zapytanie *na* partycję fizyczną. Azure Cosmos DB automatycznie agreguje wyniki w różnych partycjach fizycznych.

Indeksy w różnych partycjach fizycznych są niezależne od siebie nawzajem. Brak indeksu globalnego w Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Zapytanie równoległe obejmujące wiele partycji

Zestawy Azure Cosmos DB SDK w wersji 1.9.0 i nowszych obsługują opcje równoległego wykonywania zapytań. Zapytania równoległe obejmujące wiele partycji umożliwiają wykonywanie zapytań o wiele partycji z małym opóźnieniem.

Możesz zarządzać równoległym wykonywaniem zapytań przez dostrojenie następujących parametrów:

- **MaxConcurrency**: ustawia maksymalną liczbę jednoczesnych połączeń sieciowych z partycjami kontenera. Jeśli ustawisz tę właściwość na `-1` , zestaw SDK zarządza stopień równoległości. Jeśli  `MaxConcurrency` ustawiona na `0` , istnieje pojedyncze połączenie sieciowe z partycjami kontenera.

- **MaxBufferedItemCount**: wyznacza kompromis między wykorzystaniem pamięci po stronie klienta i opóźnieniem zapytań. Jeśli ta opcja zostanie pominięta lub ustawiona na wartość -1, zestaw SDK będzie zarządzać liczbą elementów buforowanych podczas równoległego wykonywania zapytań.

Ze względu na Azure Cosmos DB możliwości zrównoleglanie zapytań między partycjami, opóźnienie zapytań będzie ogólnie skalowane, a system dodaje [partycje fizyczne](partitioning-overview.md#physical-partitions). Jednak opłata za RU zostanie znacznie zwiększona w miarę wzrostu całkowitej liczby partycji fizycznych.

Po uruchomieniu zapytania obejmującego wiele partycji, zasadniczo wykonujesz oddzielne zapytanie na poszczególne partycje fizyczne. Gdy zapytania zapytań między partycjami będą używać indeksu, jeśli jest dostępny, nadal nie są niemal efektywne jako zapytania w partycji.

## <a name="useful-example"></a>Przydatny przykład

Oto analogowo, aby lepiej zrozumieć zapytania między partycjami:

Wyobraź sobie, że jesteś sterownikiem dostarczającym, która musi dostarczać pakiety do różnych złożonych elementów apartamentu. Każda złożona komórka ma listę w miejscu, w którym znajdują się wszystkie numery jednostek rezydenta. Możemy porównać każdą z elementów typu Apartment na partycję fizyczną i każdą listę z indeksem partycji fizycznej.

Można porównać zapytania w partycji i wielu partycjach przy użyciu tego przykładu:

### <a name="in-partition-query"></a>Zapytanie wewnątrz partycji

Jeśli sterownik dostarczania zna prawidłową złożoną transpozycję Apartment (partycja fizyczna), może natychmiast przetworzyć prawidłowe kompilacje. Sterownik może sprawdzić listę złożonych numerów jednostek rezydenta (indeks) i szybko dostarczyć odpowiednie pakiety. W takim przypadku sterownik nie będzie tracić czasu ani nakładu pracy na złożone zabudowane, aby sprawdzić i sprawdzić, czy istnieją odbiorcy pakietu.

### <a name="cross-partition-query-fan-out"></a>Zapytanie między partycjami (wentylator)

Jeśli sterownik dostarczania nie zna prawidłowej złożonej składowej (partycji fizycznej), muszą one być na każdym budynku i sprawdzić listę ze wszystkimi numerami jednostek rezydenta (indeksem). Po dodaniu ich do każdej z nich złożoność nadal będą mogli korzystać z listy adresów każdego z nich. Będą jednak musieli sprawdzić listę wszystkich form złożonych, niezależnie od tego, czy wszyscy adresaci pakietu są na bieżąco. Jest to sposób działania zapytań między partycjami. Chociaż mogą korzystać z indeksu (nie musi on odcinać się w każdym pojedynczym drzwi), muszą oddzielnie sprawdzać indeks dla każdej partycji fizycznej.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Zapytanie między partycjami (z zakresem do kilku partycji fizycznych)

Jeśli sterownik dostarczania wie, że wszyscy adresaci pakietu są aktywni w ramach niektórych kilku elementów złożonych, nie będą musieli dochodzić do każdego jednego. Mimo że kierowanie do kilku komórek złożonych będzie nadal wymagało więcej pracy niż odwiedzanie pojedynczego budynku, sterownik dostarczania nadal zapisuje znaczący czas i nakład pracy. Jeśli zapytanie zawiera klucz partycji w filtr ze `IN` słowem kluczowym, sprawdza tylko odpowiednie indeksy partycji fizycznej dla danych.

## <a name="avoiding-cross-partition-queries"></a>Unikanie zapytań między partycjami

W przypadku większości kontenerów jest to nieuniknione, że masz pewne zapytania obejmujące wiele partycji. Występowanie niektórych zapytań między partycjami jest OK. Niemal wszystkie operacje zapytań są obsługiwane między partycjami (zarówno kluczami partycji logicznej, jak i partycjami fizycznymi). Azure Cosmos DB ma także wiele optymalizacji w aparacie zapytań i zestawach SDK klienta do zrównoleglanie wykonywania zapytań na partycjach fizycznych.

W przypadku większości scenariuszy z dużą ilością odczytu zalecamy po prostu wybranie najbardziej typowej właściwości w filtrach zapytań. Należy również upewnić się, że klucz partycji jest zgodny z innymi [najlepszymi rozwiązaniami wyboru kluczy partycji](partitioning-overview.md#choose-partitionkey).

Unikanie zapytań między partycjami zwykle dotyczy tylko dużych kontenerów. Opłata jest naliczana co najmniej przez 2,5 RU przy każdym sprawdzeniu indeksu partycji fizycznej dla wyników, nawet jeśli żadne elementy na partycji fizycznej nie pasują do filtru zapytania. W związku z tym, jeśli istnieje tylko jedna partycja fizyczna (lub zaledwie kilka), zapytania między partycjami nie będą zużywać znacznie więcej RU niż zapytania w partycji.

Liczba partycji fizycznych jest powiązana z ilością zainicjowanego obiektu RU. Każda partycja fizyczna pozwala na maksymalnie 10 000 aprowizacji RU i może przechowywać do 50 GB danych. Azure Cosmos DB automatycznie zarządza partycjami fizycznymi. Liczba partycji fizycznych w kontenerze zależy od zainicjowanej przepływności i zużywanej pamięci masowej.

Należy unikać wykonywania zapytań między partycjami, jeśli obciążenie spełnia następujące kryteria:
- Zamierzasz mieć ponad 30 000 RU
- Planujesz przechowywanie ponad 100 GB danych

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat partycjonowania w usłudze Azure Cosmos DB:

- [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB (Syntetyczne klucze partycji w usłudze Azure Cosmos DB)](synthetic-partition-keys.md)
