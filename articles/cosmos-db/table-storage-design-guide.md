---
title: Projektowanie tabel Azure Cosmos DB na potrzeby skalowania i wydajności
description: 'Przewodnik dotyczący projektowania usługi Azure Table Storage: skalowalne i wydajne tabele w Azure Cosmos DB i Azure Table Storage'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 603c891e53e5712d489fcef8415e3db55328c9ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988443"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Przewodnik projektowania tabel usługi Azure Table Storage: Skalowalne i wydajne tabele
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę różne czynniki, w tym koszt. Jeśli posiadasz doświadczenie w projektowaniu schematów na potrzeby relacyjnych baz danych, te zagadnienia będą Ci znane. Mimo że istnieją pewne podobieństwa między usługą Azure Table Storage i modelami relacyjnymi, istnieje również wiele istotnych różnic. Te różnice prowadzą zwykle do różnych projektów, które mogą wyglądać na nieintuicyjne lub błędne dla kogoś, kto ma doświadczenie z relacyjnymi bazami danych, ale mają sens, jeśli projektowanie odbywa się na potrzeby magazynu klucz/wartość bazy danych NoSQL, na przykład usługi Table Storage.

Usługa Table Storage została zaprojektowana do obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek ("wiersze" w terminologii relacyjnej bazy danych) lub zestawy danych, które muszą obsługiwać duże ilości transakcji. W związku z tym należy myśleć o sposobie przechowywania danych i zrozumieć, jak działa magazyn tabel. Dobrze zaprojektowany magazyn danych NoSQL może pozwalać na skalowanie rozwiązań znacznie bardziej wydajnych (i niższych kosztów) niż rozwiązanie korzystające z relacyjnej bazy danych. Ten przewodnik pomoże Ci w zapewnieniu tego tematu.  

## <a name="about-azure-table-storage"></a>Informacje o usłudze Azure Table Storage
Ta sekcja zawiera najważniejsze funkcje usługi Table Storage, które są szczególnie przydatne do projektowania pod kątem wydajności i skalowalności. Jeśli dopiero zaczynasz korzystać z usługi Azure Storage i magazynu tabel, zobacz [wprowadzenie do Microsoft Azure Storage](../storage/common/storage-introduction.md) i [Zacznij korzystać z usługi Azure Table Storage przy użyciu platformy .NET](./tutorial-develop-table-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Mimo że ten przewodnik dotyczy magazynu tabel, zawiera on kilka dyskusji na temat usługi Azure queue storage i usługi Azure Blob Storage oraz jak można ich używać razem z magazynem tabel w rozwiązaniu.  

Magazyn tabel używa formatu tabelarycznego do przechowywania danych. W standardowej terminologii każdy wiersz tabeli reprezentuje jednostkę, a kolumny przechowują różne właściwości tej jednostki. Każda jednostka ma parę kluczy do unikatowego identyfikowania, a kolumna sygnatur czasowych używanej przez magazyn tabel do śledzenia czasu ostatniej aktualizacji jednostki. Pole sygnatury czasowej jest dodawane automatycznie i nie można ręcznie nadpisać sygnatury czasowej z dowolną wartością. W usłudze Table Storage jest używany ostatnio modyfikowany znacznik czasu (LMT) w celu zarządzania optymistyczną współbieżnością.  

> [!NOTE]
> Operacje interfejsu API REST usługi Table Storage zwracają również `ETag` wartość, która pochodzi od LMT. W tym dokumencie terminy ETag i LMT są używane zamiennie, ponieważ odnoszą się do tych samych danych źródłowych.  
> 
> 

W poniższym przykładzie przedstawiono prosty projekt tabeli do przechowywania jednostek pracowników i działów. Wiele przykładów przedstawionych w dalszej części tego przewodnika jest opartych na tym prostym projekcie.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Nie</td>
<td>Parter</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Cze</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Dział</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Krzysztof</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Do tej pory ten projekt wygląda podobnie do tabeli w relacyjnej bazie danych. Kluczowe różnice to obowiązkowe kolumny i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda Właściwość zdefiniowana przez użytkownika, taka jak **FirstName** lub **Age**, ma typ danych, na przykład liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. W przeciwieństwie do relacyjnej bazy danych, jednak bez schematu natura magazynu tabel oznacza, że właściwość nie musi mieć tego samego typu danych dla każdej jednostki. Aby przechowywać złożone typy danych w pojedynczej właściwości, należy użyć serializowanego formatu, takiego jak JSON lub XML. Aby uzyskać więcej informacji, zobacz [Omówienie modelu danych usługi Table Storage](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Wybór `PartitionKey` i `RowKey` ma podstawowe znaczenie dla dobrego projektu tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację `PartitionKey` i `RowKey` . Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, `PartitionKey` `RowKey` wartości i są indeksowane, aby utworzyć klastrowany indeks, który umożliwia szybkie wyszukiwanie. W usłudze Table Storage nie są jednak tworzone żadne indeksy pomocnicze, więc są to jedyne dwie właściwości indeksowane (Niektóre wzorce opisane w dalszej części pokazują, jak można obejść to oczywiste ograniczenie).  

Tabela składa się z co najmniej jednej partycji, a wiele podjętych decyzji projektowych będzie wokół wyboru odpowiednich `PartitionKey` i `RowKey` zoptymalizowanych rozwiązań. Rozwiązanie może składać się tylko z pojedynczej tabeli, która zawiera wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie ma wiele tabel. Tabele pomagają logicznie organizować jednostki i ułatwiają zarządzanie dostępem do danych przy użyciu list kontroli dostępu. Całą tabelę można usunąć przy użyciu jednej operacji magazynu.  

### <a name="table-partitions"></a>Partycje tabeli
Nazwa konta, nazwa tabeli i `PartitionKey` razem identyfikują partycję w ramach usługi magazynu, w której magazyn tabel przechowuje jednostkę. Jak również część schematu adresowania dla jednostek, partycje definiują zakres dla transakcji (zobacz sekcję w dalszej części tego artykułu, [transakcje grupy jednostek](#entity-group-transactions)) i stanowią podstawę skalowania magazynu tabel. Aby uzyskać więcej informacji na temat partycji tabeli, zobacz [Lista kontrolna wydajności i skalowalności dla usługi Table Storage](../storage/tables/storage-performance-checklist.md).  

W usłudze Table Storage pojedyncze usługi węzłów mają jedną lub większą liczbę kompletnych partycji, a usługa jest skalowana przez dynamiczne Równoważenie obciążenia partycji między węzłami. Jeśli węzeł jest w obciążeniu, usługa Table Storage może podzielić zakres partycji objętych przez ten węzeł na różne węzły. Po nawrocie ruchu magazyn tabel może scalać zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji na temat wewnętrznych szczegółów magazynu tabel, a zwłaszcza sposobu zarządzania partycjami, zobacz [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

### <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W usłudze Table Storage transakcje grupy jednostek (EGTs) są jedynym wbudowanym mechanizmem do wykonywania niepodzielnych aktualizacji w wielu jednostkach. EGTs są również nazywane *transakcjami wsadowymi*. EGTs może działać tylko na jednostkach przechowywanych w tej samej partycji (udostępniając ten sam klucz partycji w określonej tabeli), dlatego w dowolnym momencie potrzebna jest niepodzielna zachowań transakcyjnych w wielu jednostkach, upewnij się, że te jednostki znajdują się w tej samej partycji. Jest to często powód, aby zachować wiele typów jednostek w tej samej tabeli (i partycji), a nie używać wielu tabel dla różnych typów jednostek. Pojedynczy EGT może działać na maksymalnie 100 jednostkach.  Jeśli przesyłasz wiele współbieżnych EGTs do przetwarzania, należy upewnić się, że te EGTs nie działają na jednostkach, które są wspólne dla EGTs. W przeciwnym razie ryzyko opóźnienia przetwarzania.

EGTs również wprowadza potencjalne rozwiązanie do szacowania w projekcie. Użycie większej liczby partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości w przypadku żądań równoważenia obciążenia między węzłami. Jednak może to ograniczyć możliwość wykonywania przez aplikacje niepodzielnych transakcji i zapewnienia silnej spójności danych. Ponadto istnieją konkretne cele skalowalności na poziomie partycji, która może ograniczyć przepływność transakcji, które można oczekiwać dla jednego węzła.

Aby uzyskać więcej informacji na temat celów skalowalności dla kont usługi Azure Storage, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../storage/common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji dotyczących skalowalności dla magazynu tabel, zobacz [cele skalowalności i wydajności dla usługi Table Storage](../storage/tables/scalability-targets.md). W kolejnych sekcjach tego przewodnika omówiono różne strategie projektowania, które ułatwiają zarządzanie niektórymi potrzebami, takimi jak ten, i omawiamy najlepszy wybór klucza partycji na podstawie określonych wymagań aplikacji klienckiej.  

### <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności
Poniższa tabela zawiera kilka najważniejszych wartości, które należy znać podczas projektowania rozwiązania magazynu tabel:  

| Łączna pojemność konta usługi Azure Storage | 500 TB |
| --- | --- |
| Liczba tabel na koncie usługi Azure Storage |Ograniczone tylko pojemności konta magazynu. |
| Liczba partycji w tabeli |Ograniczone tylko pojemności konta magazynu. |
| Liczba jednostek w partycji |Ograniczone tylko pojemności konta magazynu. |
| Rozmiar pojedynczej jednostki |Do 1 MB i maksymalnie 255 właściwości (łącznie z `PartitionKey` , `RowKey` i `Timestamp` ). |
| Rozmiar `PartitionKey` |Ciąg o rozmiarze do 1 KB. |
| Rozmiar `RowKey` |Ciąg o rozmiarze do 1 KB. |
| Rozmiar transakcji grupy jednostek |Transakcja może obejmować maksymalnie 100 jednostek, a ładunek musi mieć rozmiar mniejszy niż 4 MB. Element EGT może aktualizować tylko raz jednostkę. |

Aby uzyskać więcej informacji, zobacz [Omówienie modelu danych Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).  

### <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogi, ale należy uwzględnić oszacowania kosztów zarówno w przypadku użycia pojemności, jak i liczby transakcji w ramach oceny dowolnego rozwiązania korzystającego z usługi Table Storage. Jednak w wielu scenariuszach przechowywanie nieznormalizowanych lub zduplikowanych danych w celu poprawy wydajności lub skalowalności rozwiązania jest prawidłowym podejściem. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Wytyczne dotyczące projektu tabel
Te listy zawierają podsumowanie najważniejszych wytycznych, które należy wziąć pod uwagę podczas projektowania tabel. Ten przewodnik zawiera więcej szczegółów w dalszej części artykułu. Wytyczne te różnią się od wytycznych, które zwykle są stosowane w przypadku projektowania relacyjnej bazy danych.  

Projektowanie magazynu tabel do wydajnego *odczytu* :

* **Projekt do wykonywania zapytań w aplikacjach z dużym obciążeniem.** Podczas projektowania tabel należy zastanowić się nad zapytaniami (szczególnie w odróżnieniu od opóźnienia), które zostaną uruchomione przed zawieszeniem, jak należy zaktualizować jednostki. Zwykle jest to wydajne i wydajne rozwiązanie.  
* **Określ zarówno `PartitionKey` , jak i `RowKey` w zapytaniach.** *Zapytania punktowe* , takie jak te, to najbardziej wydajne zapytania dotyczące magazynu tabel.  
* **Rozważ przechowywanie zduplikowanych kopii jednostek.** Magazyn tabel jest tani, więc Rozważ przechowywanie tej samej jednostki wielokrotnie (z różnymi kluczami), aby umożliwić wydajniejsze zapytania.  
* **Rozważ wyprowadzenie denormalizacji danych.** Magazyn tabel jest tani, dlatego należy rozważyć denormalizację danych. Na przykład, przechowuj jednostki podsumowujące, tak aby zapytania dotyczące danych agregowanych musieli uzyskać dostęp do pojedynczej jednostki.  
* **Użyj wartości klucza złożonego.** Jedyne klucze są `PartitionKey` i `RowKey` . Na przykład użyj wartości klucza złożonego, aby włączyć alternatywne ścieżki dostępu do jednostek.  
* **Użyj projekcji zapytania.** Możesz zmniejszyć ilość danych przesyłanych przez sieć za pomocą zapytań, które wybierają tylko potrzebne pola.  

Projektowanie magazynu tabel do wydajnego *zapisu* :  

* **Nie twórz partycji aktywnych.** Wybierz klucze, które umożliwiają rozproszenie żądań w wielu partycjach w dowolnym momencie.  
* **Unikaj wzrostu ruchu.** Dystrybuuj ruch w rozsądnym czasie i unikaj wzrostu ruchu.
* **Niekoniecznie utwórz oddzielną tabelę dla każdego typu jednostki.** Gdy wymagane są transakcje niepodzielne między typami jednostek, można przechowywać te wiele typów jednostek w tej samej partycji w tej samej tabeli.
* **Należy wziąć pod uwagę maksymalną przepływność, która ma zostać osiągnięta.** Należy pamiętać o możliwościach skalowalności w usłudze Table Storage i upewnić się, że projekt nie spowoduje przekroczenia tych elementów.  

W dalszej części tego przewodnika zobaczysz przykłady, które przenoszą wszystkie te zasady do ćwiczeń.  

## <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Magazyn tabel może być odczytywany w dużym stopniu, intensywnie napisanym zapisem lub kombinacją dwóch. Ta sekcja uwzględnia projektowanie w celu wydajnej obsługi operacji odczytu. Zwykle projekt obsługujący operacje odczytu wydajnie jest również wydajny w przypadku operacji zapisu. Podczas projektowania programu w celu obsługi operacji zapisu należy jednak uwzględnić dodatkowe zagadnienia. Są one omówione w następnej sekcji [projekt do modyfikacji danych](#design-for-data-modification).

Dobrym punktem początkowym umożliwiającym odczytywanie danych w sposób efektywny jest zaproszenie "jakie zapytania będą wymagane do uruchomienia aplikacji w celu pobrania potrzebnych danych?".  

> [!NOTE]
> W przypadku używania magazynu tabel ważne jest, aby projekt mógł zostać skorygowany z góry, ponieważ jest trudny i kosztowny do jego późniejszej zmiany. Na przykład w relacyjnej bazie danych często istnieje możliwość rozwiązywania problemów z wydajnością po prostu przez dodanie indeksów do istniejącej bazy danych. Nie jest to opcja z magazynem tabel.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Jak wybór `PartitionKey` i `RowKey` wpływ na wydajność zapytań
W poniższych przykładach założono, że magazyn tabel przechowuje jednostki pracowników z następującą strukturą (większość przykładów pomija `Timestamp` Właściwość dla przejrzystości):  

| Nazwa kolumny | Typ danych |
| --- | --- |
| `PartitionKey` (Nazwa działu) |Ciąg |
| `RowKey` (Identyfikator pracownika) |String (ciąg) |
| `FirstName` |String (ciąg) |
| `LastName` |String (ciąg) |
| `Age` |Liczba całkowita |
| `EmailAddress` |Ciąg |

Poniżej przedstawiono niektóre ogólne wytyczne dotyczące projektowania zapytań usługi Table Storage. Składnia filtru użyta w poniższych przykładach pochodzi z interfejsu API REST usługi Table Storage. Aby uzyskać więcej informacji, zobacz [jednostki zapytań](/rest/api/storageservices/Query-Entities).  

* *Zapytanie punktowe* jest najbardziej wydajnym wyszukiwaniem do użycia i jest zalecane w przypadku wyszukiwania lub wyszukiwania wysokiego poziomu, które wymaga najmniejszego opóźnienia. Takie zapytanie może służyć do wydajnego lokalizowania pojedynczej jednostki przez określenie `PartitionKey` `RowKey` wartości i. Na przykład: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Druga Najlepsza to *zapytanie zakresowe*. Używa `PartitionKey` i filtrów dla zakresu `RowKey` wartości, aby zwrócić więcej niż jedną jednostkę. `PartitionKey`Wartość identyfikuje konkretną partycję, a `RowKey` wartości identyfikują podzestaw jednostek w tej partycji. Na przykład: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Trzecia Najlepsza to *skanowanie partycji*. Używa on `PartitionKey` i filtruje dla innej właściwości niebędącej kluczem i może zwrócić więcej niż jedną jednostkę. `PartitionKey`Wartość identyfikuje konkretną partycję, a wartości właściwości wybierają podzbiór jednostek w tej partycji. Na przykład: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* *Skanowanie tabeli* nie obejmuje `PartitionKey` i jest niewydajne, ponieważ przeszukuje wszystkie partycje wchodzące w skład tabeli pod kątem pasujących jednostek. Wykonuje skanowanie tabeli niezależnie od tego, czy filtr używa `RowKey` . Na przykład: `$filter=LastName eq 'Jones'`.  
* Zapytania usługi Azure Table Storage zwracające wiele jednostek sortują je w `PartitionKey` `RowKey` kolejności i. Aby uniknąć tworzenia obiektów w kliencie, należy wybrać `RowKey` , który definiuje najbardziej typowy porządek sortowania. Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Azure Table Storage](table-api-faq.md#table-api-vs-table-storage).

Użycie "**or**" do określenia filtru na podstawie `RowKey` wartości powoduje skanowanie partycji i nie jest traktowane jako zapytanie zakresu. W związku z tym Unikaj zapytań używających filtrów, takich jak: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')` .  

Aby zapoznać się z przykładami kodu po stronie klienta, które używają biblioteki klienta usługi Storage do uruchamiania wydajnych zapytań, zobacz:  

* [Uruchamianie zapytania punktu przy użyciu biblioteki klienta usługi Storage](#run-a-point-query-by-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projekcja po stronie serwera](#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, które mogą obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli, zobacz:  

* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Wybierz odpowiednią `PartitionKey`
Wybór `PartitionKey` powinien zrównoważyć potrzebę umożliwienia używania EGTs (w celu zapewnienia spójności) przed wymaganiem dystrybucji jednostek na wielu partycjach (aby zapewnić skalowalne rozwiązanie).  

Z jednej najwyższej wartości można przechowywać wszystkie jednostki w jednej partycji. Może to jednak ograniczyć skalowalność rozwiązania i uniemożliwić usłudze Table Storage możliwość równoważenia obciążenia żądań. Z drugiej najwyższej wartości można przechowywać jedną jednostkę na partycję. Jest to wysoce skalowalne i umożliwia usłudze Table Storage Równoważenie obciążenia żądań, ale uniemożliwia korzystanie z transakcji grupy jednostek.  

Idealne rozwiązanie `PartitionKey` pozwala korzystać z wydajnych zapytań i ma wystarczającą liczbę partycji, aby zapewnić skalowalność rozwiązania. Zazwyczaj można sprawdzić, czy jednostki będą mieć odpowiednią właściwość, która dystrybuuje jednostki w wystarczającej partycji.

> [!NOTE]
> Na przykład w systemie, który przechowuje informacje o użytkownikach lub pracownikach, `UserID` może być dobry `PartitionKey` . Może istnieć kilka jednostek, które używają określonego `UserID` jako klucza partycji. Każda jednostka, która przechowuje dane dotyczące użytkownika, jest zgrupowana w jedną partycję. Te jednostki są dostępne za pośrednictwem EGTs, podczas gdy nadal są wysoce skalowalne.
> 
> 

Istnieją dodatkowe zagadnienia dotyczące `PartitionKey` sposobu wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz artykuł [projektowanie pod kątem modyfikacji danych](#design-for-data-modification) w dalszej części tego artykułu.  

### <a name="optimize-queries-for-table-storage"></a>Optymalizowanie zapytań dotyczących usługi Table Storage
Magazyn tabel automatycznie indeksuje jednostki przy użyciu `PartitionKey` wartości i `RowKey` w pojedynczym indeksie klastrowanym. Wynika to z tego, że zapytania punktowe są najbardziej wydajne do użycia. Nie ma jednak żadnych indeksów, które są inne niż w indeksie klastrowanym w `PartitionKey` i `RowKey` .

Wiele projektów musi spełniać wymagania, aby umożliwić wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład lokalizowanie jednostek pracowników na podstawie poczty e-mail, identyfikatora pracownika lub nazwiska. Poniższe wzorce przedstawione w sekcji [wzorce projektowe tabeli](#table-design-patterns) dotyczą tych typów wymagań. Wzorce opisują także sposoby obejścia faktu, że magazyn tabel nie udostępnia indeksów pomocniczych.  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern): Przechowuj wiele kopii każdej jednostki przy użyciu różnych `RowKey` wartości (w tej samej partycji). Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern): przechowywanie wielu kopii każdej jednostki przy użyciu różnych `RowKey` wartości w oddzielnych partycjach lub w oddzielnych tabelach. Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec jednostek indeksu](#index-entities-pattern): utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

### <a name="sort-data-in-table-storage"></a>Sortowanie danych w usłudze Table Storage

Usługa Table Storage zwraca wyniki zapytania posortowane w kolejności rosnącej, oparte na, `PartitionKey` a następnie przez `RowKey` .

> [!NOTE]
> Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Azure Table Storage](table-api-faq.md#table-api-vs-table-storage).

Klucze w magazynie tabel są wartościami ciągu. Aby upewnić się, że wartości liczbowe są sortowane prawidłowo, należy przekonwertować je na stałą długość i uzupełnić je zerami. Na przykład jeśli wartość identyfikatora pracownika, która będzie używana jako `RowKey` wartość będąca liczbą całkowitą, należy przekonwertować pracownika o identyfikatorze **123** do **00000123**. 

Wiele aplikacji ma wymagania dotyczące korzystania z danych posortowanych w różnych zamówieniach: na przykład sortowanie pracowników według nazwy lub dołączanie daty. Następujące wzorce w [wzorach projektu tabeli](#table-design-patterns) sekcji przedstawiają sposób alternatywnych zamówień sortowania dla jednostek:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern): Przechowuj wiele kopii każdej jednostki przy użyciu różnych `RowKey` wartości (w tej samej partycji). Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern): przechowywanie wielu kopii każdej jednostki przy użyciu różnych `RowKey` wartości w oddzielnych partycjach w oddzielnych tabelach. Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.
* [Wzorzec końca dziennika](#log-tail-pattern): pobranie *n* jednostek, które zostały ostatnio dodane do partycji, przy użyciu `RowKey` wartości, która jest sortowana w odwrotnej kolejności daty i godziny.  

## <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ta sekcja koncentruje się na zagadnieniach projektowych dotyczących optymalizacji operacji wstawiania, aktualizacji i usuwania. W niektórych przypadkach należy oszacować handel między projektami, które optymalizują się w celu wykonywania zapytań dotyczących projektów, które optymalizują się pod kątem modyfikacji danych. Ta ocena jest podobna do działania w projektach relacyjnych baz danych (chociaż techniki zarządzania projektami są inne w relacyjnej bazie danych). W sekcjach [wzorów projektowania tabeli](#table-design-patterns) opisano niektóre szczegółowe wzorce projektowe usługi Table Storage, a niektóre z nich różnią się. W ćwiczeniu można sprawdzić, czy wiele projektów zoptymalizowanych pod kątem wykonywania zapytań dotyczących jednostek również działa prawidłowo w przypadku modyfikowania jednostek.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizowanie wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć jednostkę, musisz mieć możliwość zidentyfikowania jej przy użyciu `PartitionKey` `RowKey` wartości i. W związku z tym wybór `PartitionKey` i `RowKey` w celu zmodyfikowania jednostek powinien spełniać podobne kryteria, aby obsługiwać zapytania dotyczące punktów. Chcesz identyfikować jednostki tak efektywnie, jak to możliwe. Nie chcesz korzystać z niewydajnej partycji lub skanowania tabeli, aby zlokalizować jednostkę w celu odnalezienia `PartitionKey` i `RowKey` wartości, które należy zaktualizować lub usunąć.  

Następujące wzorce w [wzorach projektu tabeli](#table-design-patterns) sekcji dotyczą optymalizacji wydajności operacji INSERT, Update i DELETE:  

* [Wzorzec usuwania dużych woluminów](#high-volume-delete-pattern): Włącz usuwanie dużej liczby jednostek przez zapisanie wszystkich jednostek do jednoczesnego usunięcia w osobnej tabeli. Obiekty można usunąć, usuwając tabelę.  
* [Wzorzec serii danych](#data-series-pattern): Przechowuj kompletne serie danych w pojedynczej jednostce, aby zminimalizować liczbę żądań, które należy wykonać.  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern): Użyj wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  
* [Wzorzec dużych jednostek](#large-entities-pattern): Użyj magazynu obiektów BLOB do przechowywania dużych wartości właściwości.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnianie spójności przechowywanych jednostek
Drugim czynnikiem, który ma wpływ na wybór kluczy do optymalizacji modyfikacji danych, jest sposób zapewnienia spójności przy użyciu transakcji niepodzielnych. Elementu EGT można używać tylko do obsługi jednostek przechowywanych w tej samej partycji.  

W poniższych wzorcach [wzorców projektu tabeli](#table-design-patterns) sekcji adres zarządzania spójnością:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern): Przechowuj wiele kopii każdej jednostki przy użyciu różnych `RowKey` wartości (w tej samej partycji). Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern): przechowywanie wielu kopii każdej jednostki przy użyciu różnych `RowKey` wartości w oddzielnych partycjach lub w oddzielnych tabelach. Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern): Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.
* [Wzorzec jednostek indeksu](#index-entities-pattern): utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  
* [Wzorzec denormalizacji](#denormalization-pattern): połącz powiązane dane ze sobą w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania z pojedynczym punktem.  
* [Wzorzec serii danych](#data-series-pattern): Przechowuj kompletne serie danych w jednej jednostce, aby zminimalizować liczbę podejmowanych żądań.  

Aby uzyskać więcej informacji, zobacz [transakcje grupy jednostek](#entity-group-transactions) w dalszej części tego artykułu.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Upewnij się, że projekt do wydajnej modyfikacji ułatwia wykonywanie wydajnych zapytań
W wielu przypadkach projekt do wydajnego wykonywania zapytań umożliwia wydajne modyfikacje, ale zawsze należy sprawdzić, czy jest to przypadek dla konkretnego scenariusza. Niektóre wzorce w [wzorach projektu tabeli](#table-design-patterns) w sekcji jawnie szacują kompromisy między wykonywaniem zapytań i modyfikowaniem jednostek, a zawsze należy wziąć pod uwagę liczbę poszczególnych typów operacji.  

Poniższe wzorce w sekcjach [projektowanie projektowe](#table-design-patterns) , które stanowią rozróżnienia między projektowaniem pod kątem wydajnych zapytań i projektowaniem do wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](#compound-key-pattern): Użyj `RowKey` wartości złożonych, aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  
* [Wzorzec końca dziennika](#log-tail-pattern): pobranie *n* jednostek, które zostały ostatnio dodane do partycji, przy użyciu `RowKey` wartości, która jest sortowana w odwrotnej kolejności daty i godziny.  

## <a name="encrypt-table-data"></a>Szyfruj dane tabeli
Biblioteka klienta usługi Azure Storage dla platformy .NET obsługuje szyfrowanie właściwości jednostki ciągów dla operacji wstawiania i zamieniania. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem do ciągów po odszyfrowaniu.    

W przypadku tabel oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Określ `EncryptProperty` atrybut (dla jednostek poco pochodzących od `TableEntity` ) lub Określ program rozpoznawania szyfrowania w opcjach żądania. Program rozpoznawania szyfrowania jest delegatem, który pobiera klucz partycji, klucz wiersza i nazwę właściwości, i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania, Biblioteka klienta używa tych informacji do określenia, czy właściwość powinna być szyfrowana podczas zapisywania w sieci. Delegat udostępnia również możliwość logiki dotyczącej sposobu szyfrowania właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Nie trzeba podawać tych informacji podczas odczytywania lub wysyłania zapytań do jednostek.

Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości mógł zostać wcześniej zaszyfrowany przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonania dodatkowych wywołań usługi do odczytania istniejącej jednostki z usługi lub użycia nowego klucza dla każdej właściwości. Żadna z tych elementów nie jest odpowiednia ze względu na wydajność.     

Aby uzyskać informacje na temat szyfrowania danych tabeli, zobacz [szyfrowanie po stronie klienta i Azure Key Vault Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Relacje modelu
Tworzenie modeli domen to kluczowy etap projektowania złożonych systemów. Zazwyczaj proces modelowania jest używany do identyfikowania jednostek i relacji między nimi w celu zrozumienia domeny biznesowej i poinformowania o projekcie systemu. Ta sekcja koncentruje się na sposobie tłumaczenia niektórych wspólnych typów relacji znalezionych w modelach domen na projekty dla magazynu tabel. Proces mapowania z logicznego modelu danych na fizyczny model danych oparty na NoSQL różni się od tego, który jest używany podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zazwyczaj zakłada proces normalizacji danych zoptymalizowany pod kątem minimalizowania nadmiarowości. Taki projekt zakłada również możliwość wykonywania zapytań deklaratywnych, która stanowi streszczenie implementacji działania bazy danych.  

### <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacja jeden do wielu między obiektami domeny biznesowej występuje często: na przykład jeden dział ma wielu pracowników. Istnieje kilka sposobów implementacji relacji jeden-do-wielu w magazynie tabel, z których każdy ma zalety i wady, które mogą dotyczyć konkretnego scenariusza.  

Rozważmy przykład dużej firmy wielonarodowej z dziesiątki tysięcy działów i jednostek pracowników. Każdy dział ma wielu pracowników, a każdy pracownik jest skojarzony z jednym określonym działem. Jednym z rozwiązań jest przechowywanie oddzielnych jednostek działu i pracowników, takich jak następujące:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="Ilustracja przedstawiająca jednostkę działu i jednostkę pracownika":::

Ten przykład pokazuje niejawną relację "jeden do wielu" między typami, na podstawie `PartitionKey` wartości. Każdy dział może mieć wielu pracowników.  

Ten przykład pokazuje również jednostkę działu i powiązane jednostki pracowników w tej samej partycji. Można wybrać użycie różnych partycji, tabel, a nawet kont magazynu dla różnych typów jednostek.  

Alternatywnym podejściem jest denormalizowanie danych i przechowywanie tylko jednostek pracowników z nieznormalizowanymi danymi działu, jak pokazano w poniższym przykładzie. W tym konkretnym scenariuszu podejście nieznormalizowane może nie być najlepszym rozwiązaniem, jeśli istnieje wymóg, aby można było zmienić szczegóły menedżera działu. W tym celu należy zaktualizować każdego pracownika w dziale.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="Ilustracja jednostki Employee, która pokazuje, jak przeprowadzić denormalizację danych i przechowywać tylko jednostki pracowników z nieznormalizowanymi danymi działu.":::

Aby uzyskać więcej informacji, zobacz [wzorzec denormalizacji](#denormalization-pattern) w dalszej części tego przewodnika.  

Poniższa tabela zawiera podsumowanie specjalistów i wad poszczególnych metod przechowywania jednostek pracowników i działów, które mają relację jeden-do-wielu. Należy również wziąć pod uwagę częstotliwość wykonywania różnych operacji. Może być możliwe zaakceptowanie projektu, który obejmuje kosztowną operację, jeśli ta operacja jest wykonywana tylko rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Zalety</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, ta sama partycja, ta sama tabela</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu przy użyciu jednej operacji.</li>
<li>Można użyć EGT, aby zachować spójność, jeśli istnieje potrzeba modyfikacji jednostki działu przy każdej aktualizacji/wstawieniu/usunięciu jednostki pracownika. Na przykład w przypadku utrzymania liczby pracowników działu dla poszczególnych działów.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Operacje magazynu są wykonywane w tej samej partycji. W przypadku dużych ilości transakcji może to spowodować powstanie hotspotu.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Oddzielne typy jednostek, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu lub jednostkę pracownika przy użyciu jednej operacji.</li>
<li>W przypadku dużych ilości transakcji może to ułatwić rozproszenie obciążenia na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Nie można użyć EGTs, aby zachować spójność podczas aktualizowania/wstawiania/usuwania pracownika i aktualizowania działu. Na przykład w jednostce działu można aktualizować liczbę pracowników.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizowanie do typu pojedynczej jednostki</td>
<td>
<ul>
<li>Wszystkie potrzebne informacje można pobrać przy użyciu pojedynczego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Zachowanie spójności może być kosztowne, jeśli trzeba zaktualizować informacje działu (wymaga to zaktualizowania wszystkich pracowników w dziale).</li>
</ul>
</td>
</tr>
</table>

Jak wybierać spośród tych opcji i które z nich są najbardziej znaczące, zależy to od konkretnych scenariuszy aplikacji. Na przykład jak często modyfikujesz jednostki działu? Czy wszystkie zapytania pracowników potrzebują dodatkowych informacji działu? Jak blisko ograniczeń skalowalności w partycjach lub na koncie magazynu?  

### <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modele domeny mogą zawierać relacje jeden do jednego między jednostkami. Jeśli zachodzi potrzeba zaimplementowania relacji jeden-do-jednego w magazynie tabel, należy również wybrać opcję łączenia dwóch powiązanych jednostek, gdy trzeba je pobrać. Ten link może być niejawny, na podstawie Konwencji w wartościach klucza lub jawne, przez zapisanie linku w formie `PartitionKey` i `RowKey` wartości w każdej jednostce względem powiązanej jednostki. Aby zapoznać się z omówieniem, czy należy przechowywać powiązane jednostki w tej samej partycji, zobacz [relacje jeden do wielu](#one-to-many-relationships).  

Istnieją także uwagi dotyczące implementacji, które mogą prowadzić do wdrożenia relacji jeden-do-jednego w magazynie tabel:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [wzorzec dużych jednostek](#large-entities-pattern)).  
* Implementowanie kontroli dostępu (Aby uzyskać więcej informacji, zobacz [Kontrola dostępu za pomocą sygnatur dostępu współdzielonego](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Dołącz do klienta
Chociaż istnieją sposoby tworzenia relacji między modelami w usłudze Table Storage, nie zapomnij, że dwa podstawowe przyczyny korzystania z usługi Table Storage to skalowalność i wydajność. Jeśli okaże się, że modelowanie ma wiele relacji, które naruszają wydajność i skalowalność rozwiązania, należy poproszony o to, jeśli konieczne jest skompilowanie wszystkich relacji danych w projekcie tabeli. Może być możliwe uproszczenie projektowania i zwiększenie skalowalności i wydajności rozwiązania, Jeśli zezwolisz aplikacji klienckiej na wykonywanie dowolnych wymaganych sprzężeń.  

Na przykład jeśli masz małe tabele zawierające dane, które nie zmieniają się często, możesz pobrać te dane raz i umieścić je w pamięci podręcznej na kliencie. Może to uniknąć powtarzanych roundtrip, aby pobrać te same dane. W przykładach, które zostały przedstawione w tym przewodniku, zestaw działów w małej organizacji może być niewielki i nierzadko zmieniany. Sprawia to, że jest to dobry kandydat dla danych, które aplikacja kliencka może pobrać raz i buforować jako dane wyszukiwania.  

### <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka korzysta z zestawu klas, które stanowią część relacji dziedziczenia reprezentującej jednostki biznesowe, można łatwo utrzymać te jednostki w magazynie tabel. Na przykład w aplikacji klienckiej można określić następujący zestaw klas, gdzie `Person` jest klasą abstrakcyjną.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="Diagram relacji dziedziczenia":::

Można utrzymywać wystąpienia dwóch konkretnych klas w usłudze Table Storage przy użyciu pojedynczej `Person` tabeli. Użyj jednostek, które wyglądają następująco:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="Ilustracja przedstawiająca jednostkę klienta i jednostkę pracownika":::

Aby uzyskać więcej informacji na temat pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta, zobacz [Praca z typami jednostek heterogenicznych](#work-with-heterogeneous-entity-types) w dalszej części tego przewodnika. Zawiera przykłady sposobu rozpoznawania typu jednostki w kodzie klienta.  

## <a name="table-design-patterns"></a>Wzorce projektowe tabel
W poprzednich sekcjach zawarto informacje na temat optymalizowania projektu tabeli na potrzeby pobierania danych jednostki przy użyciu zapytań oraz wstawiania, aktualizowania i usuwania danych jednostki. W tej sekcji opisano niektóre wzorce, które są odpowiednie do użytku z usługą Table Storage. Ponadto zobaczysz, jak można praktycznie rozwiązać niektóre problemy i wady, które zostały wcześniej zgłoszone w tym przewodniku. Poniższy diagram podsumowuje relacje między różnymi wzorcami:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="Diagram wzorców projektu tabeli":::

Mapa wzorca wyróżnia pewne relacje między wzorcami (Blue) i antywzorców (pomarańczowy), które są opisane w tym przewodniku. Istnieją oczywiście wiele innych wzorców, które warto wziąć pod uwagę. Na przykład jeden z kluczowych scenariuszy dotyczących usługi Table Storage polega na użyciu [wzorca widoku materiałowego](/previous-versions/msp-n-p/dn589782(v=pandp.10)) ze wzorca [segregowania odpowiedzialności z poleceniami](/previous-versions/msp-n-p/jj554200(v=pandp.10)) .  

### <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego wewnątrz partycji
Przechowywanie wielu kopii każdej jednostki przy użyciu różnych `RowKey` wartości (w tej samej partycji). Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości. Aktualizacje między kopiami można zachować spójność za pomocą EGTs.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki przy użyciu `PartitionKey` `RowKey` wartości i. Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład korzystając z poniższej struktury tabeli, aplikacja kliencka może użyć zapytania Point do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika ( `PartitionKey` i `RowKey` wartości). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="Ilustracja jednostki pracownika, w której aplikacja kliencka może używać zapytania punktu do pobierania poszczególnych jednostek pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości PartitionKey i RowKey).":::

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że magazyn tabel nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż `RowKey` kolejność.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii poszczególnych jednostek, z których każda ma inną `RowKey` wartość. Jeśli przechowujesz jednostkę z następującymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów dla `RowKey` , `empid_` i `email_` umożliwiają wykonywanie zapytań dotyczących jednego pracownika lub zakresu pracowników przy użyciu zakresu adresów e-mail lub identyfikatorów pracowników.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z różnymi wartościami RowKey":::

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika) i jeden przeszukiwany przez adres e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey EQ "Sales") i (RowKey EQ "empid_000223")  
* $filter = (PartitionKey EQ "Sales") i (RowKey EQ " email_jonesj@contoso.com ")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail. Zapytanie dotyczące jednostek z odpowiednim prefiksem w `RowKey` .  

* Aby znaleźć wszystkich pracowników działu sprzedaży z IDENTYFIKATORem pracownika z zakresu od 000100 do 000199, użyj: $filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000100") i (RowKey Le "empid_000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail rozpoczynającego się od litery "a", należy użyć: $filter = (PartitionKey EQ "Sales") i (RowKey GE "email_a") i (RowKey lt "email_b")  
  
Składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST usługi Table Storage. Aby uzyskać więcej informacji, zobacz [jednostki zapytań](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Magazyn tabel jest stosunkowo tani, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Należy jednak zawsze oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, które będą uruchamiane przez aplikację kliencką.  
* Ponieważ pomocnicze jednostki indeksu są przechowywane w tej samej partycji co oryginalne jednostki, należy się upewnić, że elementy docelowe skalowalności dla pojedynczej partycji nie zostaną przekroczone.  
* Zduplikowane jednostki można zachować spójnie ze sobą za pomocą EGTs, aby zaktualizować dwie kopie jednostki jako niepodzielne. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz [Korzystanie z transakcji grupy jednostek](#entity-group-transactions).  
* Wartość używana dla elementu `RowKey` musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w `RowKey` (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie w oparciu o górną i dolną granicę.  
* Nie musisz koniecznie duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w `RowKey` nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="Ilustracja jednostki Employee":::

* Zazwyczaj lepiej jest przechowywać duplikaty danych i upewnić się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania, niż używać jednego zapytania do lokalizowania jednostki i drugiej, aby wyszukać wymagane dane.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy:

- Aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy.
- Klient musi pobrać jednostki w różnych zamówieniach sortowania.
- Każdą jednostkę można zidentyfikować przy użyciu różnych unikatowych wartości.

Należy jednak pamiętać, że nie przekracza limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych `RowKey` wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern)
* [Wzorzec klucza złożonego](#compound-key-pattern)
* [Transakcje grupy jednostek](#entity-group-transactions)
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego między partycjami
Przechowywanie wielu kopii każdej jednostki przy użyciu różnych `RowKey` wartości w oddzielnych partycjach lub w oddzielnych tabelach. Pozwala to na szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych `RowKey` wartości.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki przy użyciu `PartitionKey` `RowKey` wartości i. Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład korzystając z poniższej struktury tabeli, aplikacja kliencka może użyć zapytania Point do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika ( `PartitionKey` i `RowKey` wartości). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="Ilustracja przedstawiająca strukturę jednostki pracowników, która, gdy jest używana, aplikacja kliencka może użyć zapytania punkt do pobrania poszczególnych jednostek pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości PartitionKey i RowKey)."::: 9

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że magazyn tabel nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż `RowKey` kolejność.  

Przewidujesz dużą liczbę transakcji w odniesieniu do tych jednostek i chcesz zminimalizować ryzyko związane z szybkością magazynowania tabeli ograniczającej klienta.  

#### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki, przy czym każda kopia używa różnych `PartitionKey` `RowKey` wartości. Jeśli przechowujesz jednostkę z następującymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów dla `PartitionKey` , `empid_` i `email_` umożliwiają identyfikowanie indeksu, który ma być używany w zapytaniu.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z indeksem podstawowym i jednostką pracownika z indeksem pomocniczym":::

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika) i jeden przeszukiwany przez adres e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey EQ "empid_Sales") i (RowKey EQ "000223")
* $filter = (PartitionKey EQ "email_Sales") i (RowKey EQ " jonesj@contoso.com ")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail. Zapytanie dotyczące jednostek z odpowiednim prefiksem w `RowKey` .  

* Aby znaleźć wszystkich pracowników działu sprzedaży z IDENTYFIKATORem pracownika z zakresu od **000100** do **000199**, posortowanych w kolejności identyfikatorów pracowników, użyj: $Filter = (PartitionKey EQ "empid_Sales") i (RowKey GE "000100") i (RowKey Le "000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail, który rozpoczyna się od "a", posortowanych w kolejności adresów e-mail, użyj: $filter = (PartitionKey EQ "email_Sales") i (RowKey GE "a") i (RowKey lt "b")  

Należy zauważyć, że składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST usługi Table Storage. Aby uzyskać więcej informacji, zobacz [jednostki zapytań](/rest/api/storageservices/Query-Entities).  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Zduplikowane jednostki można zachować ze sobą spójnie, korzystając ze [wzorca ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern) , aby zachować jednostki indeksu podstawowego i pomocniczego.  
* Magazyn tabel jest stosunkowo tani, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Jednak zawsze należy oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodać tylko zduplikowane jednostki do obsługi zapytań, które będą uruchamiane przez aplikację kliencką.  
* Wartość używana dla elementu `RowKey` musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w `RowKey` (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie w oparciu o górną i dolną granicę.  
* Nie musisz koniecznie duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w `RowKey` nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z dodatkowym indeksem":::

* Zazwyczaj lepiej jest przechowywać zduplikowane dane i upewnić się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania, niż używać jednego zapytania do lokalizowania jednostki przy użyciu pomocniczego indeksu i innego do wyszukiwania wymaganych danych w indeksie podstawowym.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy:

- Aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy.
- Klient musi pobrać jednostki w różnych zamówieniach sortowania.
- Każdą jednostkę można zidentyfikować przy użyciu różnych unikatowych wartości.

Użyj tego wzorca, jeśli chcesz uniknąć przekroczenia limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych `RowKey` wartości.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Wzorzec ostatecznie spójnych transakcji
Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.  

#### <a name="context-and-problem"></a>Kontekst i problem
EGTs Włącz transakcję niepodzielną w wielu jednostkach, które współużytkują ten sam klucz partycji. Ze względu na wydajność i skalowalność można zdecydować się na przechowywanie jednostek, które mają wymagania spójności w oddzielnych partycjach lub w osobnym systemie magazynu. W takim scenariuszu nie można używać EGTs, aby zachować spójność. Na przykład może istnieć wymóg zachowania spójności ostatecznej między:  

* Jednostki przechowywane w dwóch różnych partycjach w tej samej tabeli, w różnych tabelach lub na różnych kontach magazynu.  
* Jednostka przechowywana w magazynie tabel i obiekt BLOB przechowywany w usłudze BLOB Storage.  
* Jednostka przechowywana w magazynie tabel i pliku w systemie plików.  
* Jednostka przechowywana w magazynie tabel, jeszcze indeksowana przy użyciu usługi Azure Wyszukiwanie poznawcze.  

#### <a name="solution"></a>Rozwiązanie
Korzystając z kolejek platformy Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną na co najmniej dwóch partycjach lub systemach magazynowania.

Aby zilustrować to podejście, Załóżmy, że istnieje wymóg, aby móc archiwizować dawne jednostki pracowników. Wcześniejsze jednostki pracowników są rzadko badane i powinny być wykluczone z wszelkich działań, które zajmują się bieżącymi pracownikami. Aby zaimplementować to wymaganie, należy przechowywać aktywnych pracowników w **bieżącej** tabeli i byłych pracowników w tabeli **archiwum** . Archiwizowanie pracownika wymaga usunięcia jednostki z **bieżącej** tabeli i dodania jednostki do tabeli **archiwum** .

Nie można jednak wykonać tych dwóch operacji za pomocą EGT. Aby uniknąć ryzyka, że błąd powoduje, że jednostka pojawia się w obu tabelach lub żadnej z nich, operacja archiwizowania musi być ostatecznie spójna. Poniższy diagram sekwencji przedstawia kroki tej operacji.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="Diagram rozwiązania na potrzeby spójności ostatecznej":::

Klient inicjuje operację archiwizowania, umieszczając komunikat w kolejce platformy Azure (w tym przykładzie, aby zarchiwizować pracownika #456). Rola procesu roboczego sonduje kolejkę pod kątem nowych komunikatów; gdy go znajdzie, odczytuje komunikat i pozostawia ukrytą kopię w kolejce. Następnie rola proces roboczy pobiera kopię jednostki z **bieżącej** tabeli, wstawia kopię w tabeli **archiwum** , a następnie usuwa oryginalną z **bieżącej** tabeli. Na koniec w przypadku braku błędów z poprzednich kroków rola proces roboczy usuwa ukryty komunikat z kolejki.  

W tym przykładzie krok 4 na diagramie wstawia pracownika do tabeli **archiwum** . Może dodać pracownika do obiektu BLOB w magazynie obiektów blob lub w pliku w systemie plików.  

#### <a name="recover-from-failures"></a>Odzyskiwanie po awarii
Należy pamiętać, że operacje wykonywane w ramach kroków 4-5 na diagramie *idempotentne* się na wypadek, gdyby rola procesu roboczego mogła ponownie uruchomić operację archiwizowania. W przypadku korzystania z usługi Table Storage w kroku 4 należy użyć operacji INSERT lub Replace. w kroku 5 należy użyć operacji "Usuń Jeśli istnieje" w używanej bibliotece klienta. Jeśli używasz innego systemu magazynu, musisz użyć odpowiedniej operacji idempotentne.  

Jeśli rola proces roboczy nigdy nie ukończy kroku 6 na diagramie, wówczas po upływie limitu czasu komunikat zostanie wyświetlony ponownie w kolejce gotowej do przetworzenia przez rolę procesu roboczego. Rola proces roboczy może sprawdzić, ile razy wiadomość w kolejce została odczytana i, w razie potrzeby, oflagować ją jako komunikat "Trująca" w celu zbadania przez wysłanie go do oddzielnej kolejki. Aby uzyskać więcej informacji o odczytywaniu komunikatów w kolejce i sprawdzaniu liczby odgałęzień, zobacz [pobieranie komunikatów](/rest/api/storageservices/Get-Messages).  

Niektóre błędy z magazynu tabel i magazynu kolejek są błędami przejściowymi, a aplikacja kliencka powinna zawierać odpowiednią logikę ponawiania, aby je obsłużyć.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie zapewnia izolacji transakcji. Na przykład klient może odczytać **bieżące** i **archiwalne** tabele, gdy rola proces roboczy przeprowadzono między krokami 4-5 na diagramie i widzi niespójny widok danych. Dane będą ostatecznie spójne.  
* Musisz się upewnić, że kroki 4-5 są idempotentne w celu zapewnienia spójności ostatecznej.  
* Możesz skalować rozwiązanie przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz zagwarantowania ostatecznej spójności między jednostkami, które istnieją w różnych partycjach lub tabelach. Ten wzorzec można rozwinąć, aby zapewnić spójność dla operacji w ramach magazynu tabel i magazynu obiektów blob oraz innych źródeł danych niezwiązanych z usługą Azure Storage, takich jak baza danych lub system plików.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Scal lub Zamień](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacja transakcji jest ważna dla Twojego rozwiązania, rozważ przeprojektowanie tabel, aby umożliwić korzystanie z EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Zachowaj jednostki indeksu, aby włączyć wydajne wyszukiwania zwracające listy jednostek.  

#### <a name="context-and-problem"></a>Kontekst i problem
Magazyn tabel automatycznie indeksuje jednostki przy użyciu `PartitionKey` `RowKey` wartości i. Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu zapytania punktowego. Na przykład przy użyciu poniższej struktury tabeli aplikacja kliencka może efektywnie pobrać poszczególne jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika ( `PartitionKey` i `RowKey` ).  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="Ilustracja przedstawiająca strukturę jednostki pracowników, w której aplikacja kliencka może efektywnie pobrać indywidualną jednostkę pracownika przy użyciu nazwy działu i identyfikatora pracownika (PartitionKey i RowKey).":::

Jeśli chcesz również mieć możliwość pobrania listy jednostek pracowników na podstawie wartości innej nieunikatowej właściwości, takiej jak nazwisko, należy użyć mniej wydajnego skanowania partycji. To skanowanie wyszukuje dopasowania, zamiast używać indeksu, aby wyszukiwać je bezpośrednio. Wynika to z faktu, że magazyn tabel nie udostępnia indeksów pomocniczych.  

#### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska z poprzednią strukturą jednostki, należy zachować listy identyfikatorów pracowników. Jeśli chcesz pobrać jednostki pracownika z określoną nazwiskiem, np. Nowak, należy najpierw znaleźć listę identyfikatorów pracowników dla pracowników, którzy mają nazwisko Kowalski, a następnie pobrać te jednostki pracowników. Istnieją trzy główne opcje przechowywania list identyfikatorów pracowników:  

* Użyj magazynu obiektów BLOB.  
* Utwórz jednostki indeksu w tej samej partycji co jednostki pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

Opcja 1. Korzystanie z magazynu obiektów BLOB  

Utwórz obiekt BLOB dla każdej unikatowej nazwy, a w każdym z nich znajduje się lista `PartitionKey` wartości (dział) i `RowKey` (identyfikator pracownika) dla pracowników, którzy mają tę ostatnią nazwę. Po dodaniu lub usunięciu pracownika upewnij się, że zawartość odpowiedniego obiektu BLOB jest ostatecznie spójna z jednostkami pracownika.  

Opcja 2. Tworzenie jednostek indeksu w tej samej partycji  

Użyj obiektów index, które przechowują następujące dane:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z ciągiem zawierającym listę identyfikatorów pracowników o tej samej nazwie":::

`EmployeeIDs`Właściwość zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w `RowKey` .  

Poniższe kroki przedstawiają proces, który należy wykonać podczas dodawania nowego pracownika. W tym przykładzie dodajemy pracownika o IDENTYFIKATORze 000152 i nazwisko Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu o `PartitionKey` wartości "Sales" i `RowKey` wartości "Nowak". Zapisz element ETag tej jednostki do użycia w kroku 2.  
2. Utwórz transakcję grupy jednostek (czyli operację wsadową), która wstawia nową jednostkę pracownika ( `PartitionKey` wartość "Sales" i `RowKey` wartość "000152"), a następnie aktualizuje jednostkę indeksu ( `PartitionKey` wartość "Sales" i `RowKey` wartość "Nowak"). EGT to przez dodanie nowego identyfikatora pracownika do listy w polu Idpracownikas. Aby uzyskać więcej informacji na temat EGTs, zobacz [transakcje grupy jednostek](#entity-group-transactions).  
3. Jeśli EGT nie powiedzie się z powodu optymistycznego błędu współbieżności (oznacza to, że ktoś inny zmodyfikował jednostkę indeksu), należy zacząć od nowa w kroku 1.  

Jeśli używasz drugiej opcji, możesz użyć podobnej metody do usuwania pracownika. Zmiana nazwiska pracownika jest nieco bardziej złożona, ponieważ należy uruchomić EGT, który aktualizuje trzy jednostki: jednostki pracownika, jednostki indeksu dla starej nazwy i jednostki indeksu dla nowej nazwy. Przed wprowadzeniem jakichkolwiek zmian należy pobrać każdą jednostkę, aby pobrać wartości ETag, których można użyć do przeprowadzenia aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy konieczne jest wyszukanie wszystkich pracowników z określoną nazwiskiem w dziale. W tym przykładzie szukamy wszystkich pracowników o nazwisku Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu o `PartitionKey` wartości "Sales" i `RowKey` wartości "Nowak".  
2. Przeanalizuj listę identyfikatorów pracowników w `EmployeeIDs` polu.  
3. Jeśli potrzebujesz dodatkowych informacji na temat każdego z tych pracowników (takich jak adresy e-mail), Pobierz każdą jednostkę pracownika przy użyciu `PartitionKey` wartości "Sales" i `RowKey` wartości z listy pracowników uzyskanych w kroku 2.  

Opcja 3: tworzenie jednostek indeksu w oddzielnej partycji lub tabeli  

W przypadku tej opcji Użyj jednostek indeksu, które przechowują następujące dane:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="Zrzut ekranu pokazujący jednostkę indeksu pracownika, która zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w RowKey i PartitionKey.":::

`EmployeeDetails`Właściwość zawiera listę identyfikatorów pracowników i par nazw działów dla pracowników, których nazwisko jest przechowywane w `RowKey` .

Nie można użyć EGTs do zachowania spójności, ponieważ jednostki indeksu znajdują się w oddzielnej partycji od jednostek pracowników. Upewnij się, że jednostki indeksu są ostatecznie spójne z jednostkami pracowników.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań, aby pobrać pasujące jednostki: jeden do zapytania o jednostki indeksu w celu uzyskania listy `RowKey` wartości, a następnie wysyła zapytanie w celu pobrania każdej jednostki z listy.  
* Ponieważ każda jednostka ma maksymalny rozmiar 1 MB, opcja 2 i opcja 3 w rozwiązaniu założono, że lista identyfikatorów pracowników dla każdej z określonych nazwisk nie ma więcej niż 1 MB. Jeśli lista identyfikatorów pracowników może mieć rozmiar większy niż 1 MB, użyj opcji 1 i Zapisz dane indeksu w usłudze BLOB Storage.  
* Jeśli używasz opcji 2 (przy użyciu EGTs do obsługi dodawania i usuwania pracowników i zmieniania nazwiska pracownika), należy oszacować, czy liczba transakcji będzie zbliżać się do ograniczeń skalowalności określonej partycji. W takim przypadku należy wziąć pod uwagę ostatecznie spójne rozwiązanie (opcja 1 lub 3). Te kolejki służą do obsługi żądań aktualizacji i umożliwiają przechowywanie jednostek indeksu w oddzielnej partycji od jednostek pracowników.  
* Opcja 2 w tym rozwiązaniu zakłada, że chcesz wyszukać według nazwiska w ramach działu. Na przykład chcesz pobrać listę pracowników o nazwisku Nowak w dziale sprzedaży. Jeśli chcesz mieć możliwość wyszukania wszystkich pracowników o nazwisku Nowak w całej organizacji, użyj opcji 1 lub Option 3.
* Możesz zaimplementować rozwiązanie oparte na kolejce, które zapewnia spójność ostateczną. Aby uzyskać więcej informacji, zobacz [wzorce ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz wyszukać zestaw jednostek, które współużytkują wspólną wartość właściwości, na przykład wszystkich pracowników o nazwisku Nowak.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Wzorzec denormalizacji
Połącz powiązane dane razem w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania pojedynczego punktu.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizuje dane w celu usunięcia duplikatów, które występuje, gdy zapytania pobierają dane z wielu tabel. Jeśli normalizuje dane w tabelach platformy Azure, musisz wykonać wiele rund z klienta do serwera, aby pobrać powiązane dane. Na przykład w przypadku poniższej struktury tabeli potrzebne są dwie rundy, aby pobrać szczegóły dotyczące działu. Jedna podróż pobiera jednostkę działu, która zawiera identyfikator Menedżera, a druga — w przypadku pobierania szczegółów kierownika w jednostce pracownika.  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="Ilustracja przedstawiająca jednostkę działu i jednostkę pracownika":::

#### <a name="solution"></a>Rozwiązanie
Zamiast przechowywania danych w dwóch osobnych jednostkach, należy deznormalizować dane i zachować kopię szczegółów kierownika w jednostce działu. Na przykład:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="Ilustracja nieznormalizowanej i połączonej jednostki działu":::

W przypadku jednostek działu przechowywanych z tymi właściwościami można teraz pobrać wszystkie wymagane szczegóły dotyczące działu przy użyciu zapytania punktowego.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje kilka kosztów związanych z magazynowaniem danych. Korzyść wydajności wynikająca z mniejszej liczby żądań do usługi Table Storage zazwyczaj zmniejsza krańcowy wzrost kosztów magazynowania. Dodatkowo ten koszt jest częściowo przesunięty o obniżkę liczby transakcji wymaganych do pobrania szczegółów działu.  
* Należy zachować spójność dwóch jednostek, które przechowują informacje o menedżerach. Problem ze spójnością można obsłużyć, korzystając z EGTs do aktualizowania wielu jednostek w pojedynczej niepodzielnej transakcji. W takim przypadku jednostka działu i jednostka Employee dla menedżera działu są przechowywane w tej samej partycji.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy często potrzebujesz wyszukać powiązane informacje. Ten wzorzec zmniejsza liczbę zapytań, które klient musi wykonać, aby pobrać wymagane dane.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Wzorzec klucza złożonego
Użyj `RowKey` wartości złożonych, aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  

#### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych można używać sprzężeń w zapytaniach, aby zwrócić powiązane fragmenty danych do klienta w jednym zapytaniu. Na przykład możesz użyć identyfikatora pracownika, aby wyszukać listę powiązanych jednostek, które zawierają dane dotyczące wydajności i przeglądu dla tego pracownika.  

Załóżmy, że przechowujesz jednostki pracowników w usłudze Table Storage, korzystając z następującej struktury:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="Ilustracja przedstawiająca strukturę jednostki pracowników, która powinna być używana do przechowywania jednostek pracowników w usłudze Table Storage.":::

Należy również przechowywać dane historyczne dotyczące przeglądów i wydajności dla każdego roku, dla których pracownik pracował w organizacji, i musi mieć dostęp do tych informacji przez rok. Jedną z opcji jest utworzenie innej tabeli, która przechowuje jednostki o następującej strukturze:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="Ilustracja przedstawiająca jednostkę przeglądu pracownika":::

Korzystając z tego podejścia, możesz zdecydować się na zduplikowanie niektórych informacji (takich jak imię i nazwisko) w nowej jednostce, aby umożliwić pobieranie danych za pomocą pojedynczego żądania. Nie można jednak zachować silnej spójności, ponieważ nie można użyć EGT do aktualizowania dwóch jednostek.  

#### <a name="solution"></a>Rozwiązanie
Zapisz nowy typ jednostki w oryginalnej tabeli przy użyciu jednostek o następującej strukturze:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z kluczem złożonym":::

Zwróć uwagę `RowKey` , jak teraz jest kluczem złożonym, składającym się z identyfikatora pracownika i roku danych przeglądu. Pozwala to na pobieranie wydajności pracownika i przeglądanie danych za pomocą pojedynczego żądania dla jednej jednostki.  

Poniższy przykład przedstawia, w jaki sposób można pobrać wszystkie dane dotyczące przeglądu określonego pracownika (na przykład Employee 000123 w dziale sprzedaży):  

$filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000123") i (RowKey lt "empid_000124") &$select = RowKey, klasyfikacja Menedżera, klasyfikacja elementu równorzędnego, Komentarze  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego znaku separatora, który ułatwia przeanalizowanie `RowKey` wartości: na przykład **000123_2012**.  
* Ta jednostka jest również przechowywana w tej samej partycji co inne jednostki, które zawierają powiązane dane dla tego samego pracownika. Oznacza to, że można użyć EGTs, aby zachować silną spójność.
* Należy zastanowić się, jak często będą wykonywane zapytania o dane, aby określić, czy ten wzorzec jest odpowiedni. Na przykład, Jeśli uzyskujesz dostęp do danych przeglądu rzadko, a główne dane pracowników często, powinny być przechowywane jako osobne jednostki.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Tego wzorca należy używać, gdy konieczne jest przechowywanie co najmniej jednej powiązanej jednostki.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)  
* [Współpraca z niejednorodnymi typami jednostek](#work-with-heterogeneous-entity-types)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Wzorzec końca dziennika
Pobierz jednostki *n* ostatnio dodane do partycji przy użyciu `RowKey` wartości, która jest sortowana w odwrotnej kolejności daty i godziny.  

> [!NOTE]
> Wyniki zapytania zwrócone przez interfejs API tabel platformy Azure w Azure Cosmos DB nie są posortowane według klucza partycji lub klucza wiersza. W ten sposób, chociaż ten wzorzec jest odpowiedni dla magazynu tabel, nie jest odpowiedni dla Azure Cosmos DB. Aby uzyskać szczegółową listę różnic między funkcjami, zobacz [różnice między interfejs API tabel w Azure Cosmos DB i Table Storage platformy Azure](table-api-faq.md#table-api-vs-table-storage).

#### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymaganiem jest możliwość pobrania ostatnio utworzonych jednostek, na przykład dziesięciu ostatnich oświadczeń wydatków przesłanych przez pracownika. Zapytania tabeli obsługują `$top` operację zapytania, aby zwrócić pierwsze *n* jednostek z zestawu. Brak równoważnej operacji zapytania do zwrócenia ostatnich *n* jednostek w zestawie.  

#### <a name="solution"></a>Rozwiązanie
Zastanów się, gdy obiekty są przechowywane przy użyciu `RowKey` sortowania w odwrotnej kolejności daty/godziny, więc ostatni wpis jest zawsze pierwszym z nich w tabeli.  

Na przykład, aby można było pobrać dziesięć najnowszych oświadczeń wydatków przedstawionych przez pracownika, można użyć wartości odwróconej osi pochodnej od bieżącej daty/godziny. Poniższy przykładowy kod w języku C# przedstawia jeden ze sposobów tworzenia odpowiednich "odwróconych" znaczników dla `RowKey` sortowania, które są sortowane od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Możesz wrócić do wartości daty/godziny przy użyciu następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zapewnić, że wartość ciągu będzie sortowana zgodnie z oczekiwaniami, należy uzupełnić wartość odwrotnej osi.  
* Należy pamiętać o obiektach docelowych skalowalności na poziomie partycji. Należy zachować ostrożność, aby nie tworzyć partycji punktu aktywnego.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz uzyskać dostęp do jednostek w odwrotnej kolejności daty/godziny lub gdy potrzebujesz dostępu do ostatnio dodanych jednostek.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Poprzedź/Dołącz Antywzorzec](#prepend-append-anti-pattern)  
* [Pobierz jednostki](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Wzorzec usuwania dużych ilości
Włącz usuwanie dużej liczby jednostek, przechowując wszystkie jednostki w celu jednoczesnego usunięcia w oddzielnych tabelach. Obiekty można usunąć, usuwając tabelę.  

#### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji usuwa stare dane, które nie muszą już być dostępne dla aplikacji klienckiej, lub że aplikacja została zarchiwizowana na innym nośniku magazynowania. Dane te są zwykle identyfikowane według daty. Na przykład istnieje wymóg usuwania rekordów wszystkich żądań logowania, które są starsze niż 60 dni.  

Jednym z możliwych wzorów jest użycie daty i godziny żądania logowania w `RowKey` :  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="Ilustracja przedstawiająca jednostkę próby zalogowania":::

To podejście pozwala uniknąć hotspotów partycji, ponieważ aplikacja może wstawiać i usuwać jednostki logowania dla każdego użytkownika w oddzielnej partycji. Jednak takie podejście może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek. Najpierw należy przeprowadzić skanowanie tabeli, aby zidentyfikować wszystkie jednostki do usunięcia, a następnie usunąć każdą starą jednostkę. Można zmniejszyć liczbę operacji okrężnych do serwera wymaganego do usunięcia starych jednostek przez przetwarzanie wsadowe wielu żądań Delete do EGTs.  

#### <a name="solution"></a>Rozwiązanie
Użyj oddzielnej tabeli dla każdego dnia prób logowania. Możesz użyć powyższego projektu jednostki, aby uniknąć hotspotów podczas wstawiania jednostek. Usunięcie starych jednostek jest teraz jednym z pytań dotyczących usuwania jednej tabeli codziennie (pojedynczej operacji magazynu), zamiast znajdowania i usuwania setek i tysięcy poszczególnych jednostek logowania każdego dnia.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy projekt obsługuje inne sposoby używania danych przez aplikację, takie jak wyszukiwanie konkretnych jednostek, łączenie z innymi danymi lub generowanie zagregowanych informacji?  
* Czy projekt nie pozwala na aktywne plamy podczas wstawiania nowych jednostek?  
* Należy spodziewać się opóźnienia, jeśli chcesz użyć tej samej nazwy tabeli po jej usunięciu. Lepiej jest zawsze używać unikatowych nazw tabel.  
* Należy oczekiwać pewnej szybkości ograniczania przy pierwszym użyciu nowej tabeli, podczas gdy usługa Table Storage uzyskuje wzorce dostępu i dystrybuuje partycje między węzłami. Należy zastanowić się, jak często należy utworzyć nowe tabele.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Tego wzorca należy używać w przypadku dużej ilości jednostek, które należy usunąć w tym samym czasie.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Modyfikowanie jednostek](#modify-entities)  

### <a name="data-series-pattern"></a>Wzorzec serii danych
Przechowuj kompletne serie danych w jednej jednostce, aby zminimalizować liczbę żądań, które należy wykonać.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz polega na tym, że aplikacja przechowuje serię danych, które zazwyczaj muszą zostać pobrane jednocześnie. Na przykład aplikacja może rejestrować liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika co godzinę, a następnie użyć tych informacji do wykreślania liczby komunikatów wysyłanych przez każdego użytkownika w ciągu ostatnich 24 godzin. Jednym z projektów może być przechowywanie 24 jednostek dla każdego pracownika:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="Ilustracja przedstawiająca jednostkę statystyk komunikatów":::

Dzięki temu projektowi można łatwo zlokalizować i zaktualizować jednostkę do aktualizacji dla każdego pracownika, gdy aplikacja musi zaktualizować wartość liczby komunikatów. Aby jednak pobrać informacje w celu wykreślenia wykresu aktywności przez poprzednie 24 godziny, należy pobrać 24 jednostki.  

#### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z osobną właściwością do przechowywania liczby komunikatów dla każdej godziny:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="Ilustracja przedstawiająca jednostkę statystyki komunikatu z właściwościami rozdzielonymi":::

W tym projekcie możesz użyć operacji scalania, aby zaktualizować liczbę komunikatów dla pracownika przez określoną godzinę. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli kompletna seria danych nie mieści się w pojedynczej jednostce (jednostka może mieć do 252 właściwości), użyj alternatywnego magazynu danych, takiego jak obiekt BLOB.  
* Jeśli wielu klientów jednocześnie aktualizuje jednostkę, użyj elementu **ETag** , aby zaimplementować optymistyczną współbieżność. Jeśli masz wielu klientów, może wystąpić wysoka rywalizacja.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz zaktualizować i pobrać serie danych skojarzone z konkretną jednostką.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scal lub Zamień](#merge-or-replace)  
* [Wzorzec z ostatecznie spójnymi transakcjami](#eventually-consistent-transactions-pattern) (Jeśli przechowujesz serie danych w obiekcie BLOB)  

### <a name="wide-entities-pattern"></a>Wzorzec szerokiej jednostki
Użyj wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka może mieć nie więcej niż 252 właściwości (z wyłączeniem obowiązkowych właściwości systemu) i nie może zawierać więcej niż 1 MB danych. W relacyjnej bazie danych, zazwyczaj można obejść limity rozmiaru wiersza przez dodanie nowej tabeli i wymuszenie relacji 1-do-1 między nimi.  

#### <a name="solution"></a>Rozwiązanie
Za pomocą usługi Table Storage można przechowywać wiele jednostek do reprezentowania pojedynczego dużego obiektu biznesowego, który ma więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika przez ostatnie 365 dni, możesz użyć następującego projektu, który używa dwóch jednostek z różnymi schematami:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="Ilustracja przedstawiająca jednostkę statystyki komunikatu z Rowkey 01 i jednostką statystyki komunikatu z Rowkey 02":::

Jeśli musisz wprowadzić zmianę, która wymaga aktualizacji obu jednostek, aby zachować ich synchronizację ze sobą, możesz użyć EGT. W przeciwnym razie można użyć pojedynczej operacji scalania, aby zaktualizować liczbę komunikatów w określonym dniu. Aby pobrać wszystkie dane dla danego pracownika, należy pobrać obie jednostki. Można to zrobić z dwoma wydajnymi żądaniami, które używają zarówno `PartitionKey` wartości, jak i `RowKey` .  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Przed podjęciem decyzji o sposobie wdrożenia tego wzorca należy wziąć pod uwagę następujący punkt:  

* Pobieranie kompletnej jednostki logicznej obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania każdej jednostki fizycznej.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz przechowywać jednostki, których rozmiar lub liczba właściwości przekraczają limity dla poszczególnych jednostek w magazynie tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Transakcje grupy jednostek](#entity-group-transactions)
* [Scal lub Zamień](#merge-or-replace)

### <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Przechowywanie dużych wartości właściwości przy użyciu magazynu obiektów BLOB.  

#### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka nie może przechowywać więcej niż 1 MB danych. Jeśli jedna lub kilka właściwości magazynu wartości, które powodują, że całkowity rozmiar jednostki przekracza tę wartość, nie można przechowywać całej jednostki w magazynie tabel.  

#### <a name="solution"></a>Rozwiązanie
Jeśli rozmiar jednostki przekracza 1 MB, ponieważ co najmniej jedna z właściwości zawiera dużą ilość danych, można przechowywać dane w magazynie obiektów blob, a następnie przechowywać adres obiektu BLOB we właściwości w jednostce. Na przykład można przechowywać zdjęcie pracownika w usłudze BLOB Storage i przechowywać link do zdjęcia we `Photo` właściwości jednostki pracownika:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z ciągiem dla zdjęć wskazujących na obiekt BLOB Storage":::

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczną między jednostką w magazynie tabel i danymi w usłudze BLOB Storage, należy użyć [wzorca spójnych transakcji](#eventually-consistent-transactions-pattern) do obsługi jednostek.
* Pobieranie kompletnej jednostki obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania jednostki i jeden w celu pobrania danych obiektów BLOB.  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz przechowywać jednostki, których rozmiar przekracza limity dla poszczególnych jednostek w magazynie tabel.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Poprzedź/Dołącz Antywzorzec
W przypadku dużej ilości operacji wstawiania zwiększaj skalowalność poprzez rozłożenie operacji wstawiania na wiele partycji.  

#### <a name="context-and-problem"></a>Kontekst i problem
Oczekiwanie na lub dołączenie jednostek do przechowywanych jednostek zwykle powoduje, że aplikacja dodaje nowe jednostki do pierwszej lub ostatniej partycji sekwencji partycji. W takim przypadku wszystkie operacje wstawiania w dowolnym konkretnym czasie są realizowane w tej samej partycji, tworząc punkt aktywny. Zapobiega to korzystaniu z funkcji równoważenia obciążenia w usłudze Table Storage w wielu węzłach, co może spowodować, że aplikacja osiągnie elementy docelowe skalowalności dla partycji. Rozważmy na przykład przypadek aplikacji, która rejestruje dostęp do sieci i zasobów przez pracowników. Struktura jednostki, taka jak następujące, może spowodować, że partycja bieżąca godziny staje się hotspotem, jeśli ilość transakcji osiągnie miejsce docelowe skalowalności dla pojedynczej partycji:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="Ilustracja przedstawiająca strukturę jednostki, która może spowodować powstanie partycji bieżącej godziny jako punktu aktywnego, jeśli ilość transakcji osiągnie miejsce docelowe skalowalności dla pojedynczej partycji.":::

#### <a name="solution"></a>Rozwiązanie
Następująca alternatywna Struktura jednostek pozwala uniknąć hotspotu na określonej partycji, ponieważ aplikacja rejestruje zdarzenia:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="Ilustracja przedstawiająca jednostkę pracownika z RowKey złożonym rok, miesiąc, dzień, godzinę i identyfikator zdarzenia":::

Zwróć uwagę na to, jak `PartitionKey` i `RowKey` są kluczami złożonymi. Program `PartitionKey` używa zarówno identyfikatora działu, jak i pracownika do dystrybuowania rejestrowania między wieloma partycjami.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy struktura klucza alternatywnego, która pozwala uniknąć tworzenia aktywnych partycji w przypadku wstawiania efektywnie obsługuje zapytania, które udostępnia aplikacja kliencka?  
* Czy Przewidywana ilość transakcji oznacza, że najkorzystniej osiągniesz elementy docelowe skalowalności dla danej partycji i że zostanie ograniczona przez magazyn tabel?  

#### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać dodawania lub dołączania antywzorców, gdy ilość transakcji prawdopodobnie spowoduje ograniczenie szybkości przez magazyn tabel podczas uzyskiwania dostępu do partycji gorąca.  

#### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec końca dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Antywzorzec danych dziennika
Zazwyczaj należy używać magazynu obiektów BLOB zamiast magazynu tabel do przechowywania danych dziennika.  

#### <a name="context-and-problem"></a>Kontekst i problem
Typowy przypadek użycia dla danych dziennika polega na pobraniu wybranych wpisów dziennika dla określonego zakresu dat/godzin. Na przykład, chcesz znaleźć wszystkie komunikaty o błędach i krytyczne, które są rejestrowane przez aplikację między 15:04 a 15:06 w określonym dniu. Nie chcesz używać daty i godziny komunikatu dziennika, aby określić partycję, do której zapisywane są jednostki dziennika. Powoduje to, że w danym momencie wszystkie jednostki dziennika będą współużytkować tę samą `PartitionKey` wartość (zobacz prefiks [/dołączanie antywzorców](#prepend-append-anti-pattern)). Na przykład poniższy schemat jednostki dla komunikatu dziennika prowadzi do aktywnej partycji, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika do partycji dla bieżącej daty i godziny:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="Ilustracja przedstawiająca schemat jednostki dla komunikatu dziennika powoduje, że jest to partycja gorąca.":::

W tym przykładzie `RowKey` obejmuje datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są sortowane w kolejności daty/godziny. `RowKey`Zawiera również Identyfikator komunikatu, w przypadku którego wiele komunikatów dziennika ma taką samą datę i godzinę.  

Inna metoda polega na tym, że `PartitionKey` aplikacja będzie zapisywać komunikaty w różnych partycjach. Na przykład, jeśli źródło komunikatu dziennika zapewnia sposób dystrybucji komunikatów w wielu partycjach, można użyć następującego schematu jednostki:  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="Ilustracja przedstawiająca obiekt komunikatu dziennika":::

Jednak problem z tym schematem polega na tym, że pobranie wszystkich komunikatów dziennika dla określonego przedziału czasu wymaga przeszukania każdej partycji w tabeli.

#### <a name="solution"></a>Rozwiązanie
W poprzedniej sekcji wyróżniono problem polegający na tym, że próbujesz użyć magazynu tabel do przechowywania wpisów dziennika i sugerowanych dwóch niezadowalających projektów. Jedno rozwiązanie prowadzi do gorącej partycji z ryzykiem, w którym są zapisywane komunikaty dziennika zapisu wydajności. Inne rozwiązanie spowodowało niską wydajność zapytań, ponieważ wymaga skanowania każdej partycji w tabeli w celu pobrania komunikatów dziennika dla określonego przedziału czasu. Usługa BLOB Storage oferuje lepsze rozwiązanie dla tego typu scenariusza i jest to sposób, w jaki Analiza usługi Azure Storage przechowuje zbierane dane dziennika.  

W tej sekcji przedstawiono sposób przechowywania danych dziennika w usłudze BLOB Storage w usłudze Storage Analytics, ponieważ ilustracja tego podejścia do przechowywania danych, które są zwykle wykonywane zapytania według zakresu.  

W usłudze Storage Analytics są przechowywane komunikaty dziennika w formacie rozdzielanym w wielu obiektach Blob. Format rozdzielany ułatwia aplikacji klienckiej analizowanie danych w komunikacie dziennika.  

Analiza magazynu używa konwencji nazewnictwa obiektów blob, które umożliwiają znalezienie obiektu BLOB (lub obiektów BLOB), które zawierają komunikaty dziennika, dla których odbywa się wyszukiwanie. Na przykład obiekt BLOB o nazwie "queue/2014/07/31/1800/000001. log" zawiera komunikaty dziennika, które są powiązane z usługą kolejki przez godzinę, począwszy od dnia 18:00 lipca 2014. "000001" wskazuje, że jest to pierwszy plik dziennika dla tego okresu. Analiza magazynu rejestruje także sygnatury czasowe pierwszych i ostatnich komunikatów dziennika przechowywanych w pliku w ramach metadanych obiektu BLOB. Interfejs API usługi BLOB Storage umożliwia lokalizowanie obiektów BLOB w kontenerze na podstawie prefiksu nazwy. Aby zlokalizować wszystkie obiekty blob zawierające dane dziennika kolejki dla godziny rozpoczynającej się o 18:00, można użyć prefiksu "queue/2014/07/31/1800".  

Usługa Storage Analytics buforuje komunikaty dziennika wewnętrznie, a następnie okresowo aktualizuje odpowiedni obiekt BLOB lub tworzy nowy z najnowszymi partiami wpisów dziennika. Zmniejsza to liczbę zapisów, które muszą zostać wykonane w usłudze BLOB Storage.  

Jeśli wdrażasz podobne rozwiązanie w swojej aplikacji, weź pod uwagę sposób zarządzania handlem między niezawodnością a kosztami i skalowalnością. Innymi słowy, należy oszacować efekt zapisu każdego wpisu dziennika do magazynu obiektów blob, w porównaniu do buforowania aktualizacji w aplikacji i zapisywania ich w magazynie obiektów BLOB w partiach.  

#### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas decydowania o sposobie przechowywania danych dzienników należy wziąć pod uwagę następujące kwestie:  

* W przypadku utworzenia projektu tabeli, który pozwala uniknąć potencjalnych partycji, może się okazać, że nie można wydajnie uzyskać dostępu do danych dziennika.  
* Aby przetwarzać dane dziennika, klient często musi załadować wiele rekordów.  
* Mimo że dane dziennika są często strukturalne, usługa BLOB Storage może być lepszym rozwiązaniem.  

### <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, które należy wziąć pod uwagę podczas implementowania wzorców opisanych w poprzednich sekcjach. W większości tej sekcji są używane przykłady napisane w języku C#, które używają biblioteki klienta magazynu (wersja 4.3.0 w trakcie pisania).  

### <a name="retrieve-entities"></a>Pobierz jednostki
Jak opisano w temacie [projekt sekcji dotyczącej wykonywania zapytań](#design-for-querying), najbardziej wydajnym zapytaniem jest zapytanie punktowe. Jednak w niektórych scenariuszach może być konieczne pobranie wielu jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek przy użyciu biblioteki klienta usługi Storage.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Uruchamianie zapytania punktu przy użyciu biblioteki klienta usługi Storage
Najprostszym sposobem uruchomienia zapytania punktu jest użycie operacji **Pobierz** tabelę. Jak pokazano w poniższym fragmencie kodu w języku C#, ta operacja pobiera jednostkę o `PartitionKey` wartości "Sales" i `RowKey` wartości "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekiwana jest jednostka, która pobiera do typu `EmployeeEntity` .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Pobieranie wielu jednostek przy użyciu LINQ
Można pobrać wiele jednostek przy użyciu LINQ z biblioteką klienta usługi Storage i określić zapytanie z klauzulą **WHERE** . Aby uniknąć skanowania tabeli, należy zawsze uwzględnić `PartitionKey` wartość w klauzuli WHERE, a jeśli to możliwe, `RowKey` wartość, aby uniknąć skanowania tabeli i partycji. Magazyn tabel obsługuje ograniczony zbiór operatorów porównania (większe niż, większe niż lub równe, mniejsze niż, mniejsze niż lub równe, równe i nierówne) do użycia w klauzuli WHERE. Poniższy fragment kodu w języku C# znajduje wszystkich pracowników, których nazwisko zaczyna się od "B" (przy założeniu, że `RowKey` przechowuje ostatnią nazwę) w dziale sprzedaży (przy założeniu, że `PartitionKey` przechowuje nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Zwróć uwagę, jak zapytanie określa zarówno `RowKey` , jak i a, `PartitionKey` Aby zapewnić lepszą wydajność.  

Poniższy przykładowy kod przedstawia równoważne funkcje przy użyciu interfejsu API Fluent (Aby uzyskać więcej informacji na temat interfejsów API Fluent ogólnie, zobacz [najlepsze rozwiązania dotyczące projektowania interfejsu API Fluent](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Przykład zagnieżdża wiele `CombineFilters` metod w celu uwzględnienia trzech warunków filtru.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Pobieranie dużej liczby jednostek z zapytania
Optymalne zapytanie zwraca indywidualną jednostkę na podstawie `PartitionKey` wartości i `RowKey` wartości. Jednak w niektórych scenariuszach może być wymagane zwrócenie wielu jednostek z jednej partycji lub nawet z wielu partycji. Należy zawsze w pełni przetestować wydajność aplikacji w takich scenariuszach.  

Zapytanie dotyczące magazynu tabel może zwrócić maksymalnie 1 000 jednostek jednocześnie i działać przez maksymalnie pięć sekund. Magazyn tabel zwraca token kontynuacji, aby umożliwić aplikacji klienckiej żądanie następnego zestawu jednostek, jeśli którykolwiek z następujących warunków jest spełniony:

- Zestaw wyników zawiera więcej niż 1 000 jednostek.
- Zapytanie nie zostało ukończone w ciągu pięciu sekund.
- Zapytanie przecina granicę partycji. 

Aby uzyskać więcej informacji na temat działania tokenów kontynuacji, zobacz [limit czasu zapytania i podział na strony](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Jeśli używasz biblioteki klienta usługi Storage, może ona automatycznie obsługiwać tokeny kontynuacji, ponieważ zwraca jednostki z magazynu tabel. Na przykład następujący przykładowy kod w języku C# automatycznie obsługuje tokeny kontynuacji, jeśli magazyn tabeli zwróci je w odpowiedzi:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Poniższy kod w języku C# obsługuje jawnie tokeny kontynuacji:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Używając jawnie tokenów kontynuacji, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład, jeśli aplikacja kliencka umożliwia użytkownikom przeglądanie jednostek przechowywanych w tabeli, użytkownik może zrezygnować ze strony przez wszystkie jednostki pobrane przez zapytanie. Aplikacja będzie używać tokenu kontynuacji do pobierania następnego segmentu, gdy użytkownik zakończył stronicowanie przez wszystkie jednostki w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Można ograniczyć ilość danych pobieranych z magazynu tabel i przenoszonych przez sieć.  
* Można wykonywać asynchroniczne operacje we/wy na platformie .NET.  
* Token kontynuacji można serializować do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zwykle zwraca segment zawierający 1 000 jednostek, chociaż może zawierać mniej. Ma to również zastosowanie w przypadku ograniczenia liczby wpisów zwracanych przez **zapytanie w celu** zwrócenia pierwszych n jednostek, które pasują do kryteriów wyszukiwania. Magazyn tabel może zwracać segment zawierający mniej niż n jednostek wraz z tokenem kontynuacji, aby umożliwić pobieranie pozostałych jednostek.  
> 
> 

Poniższy kod w języku C# pokazuje, jak zmodyfikować liczbę jednostek zwróconych wewnątrz segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projekcja po stronie serwera
Jedna jednostka może mieć do 255 właściwości i mieć rozmiar maksymalnie 1 MB. Podczas wykonywania zapytania dotyczącego tabeli i pobierania jednostek mogą nie być potrzebne wszystkie właściwości i mogą uniknąć niepotrzebnych transferów danych (w celu zmniejszenia opóźnień i kosztów). Możesz użyć projekcji po stronie serwera, aby przetransferować tylko potrzebne właściwości. Poniższy przykład pobiera tylko `Email` Właściwość (wraz z,, `PartitionKey` `RowKey` `Timestamp` i `ETag` ) z jednostek wybranych przez zapytanie.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Zauważ, `RowKey` że wartość jest dostępna, nawet jeśli nie jest uwzględniona na liście właściwości do pobrania.  

### <a name="modify-entities"></a>Modyfikowanie jednostek
Biblioteka klienta magazynu umożliwia modyfikowanie jednostek przechowywanych w usłudze Table Storage przez wstawianie, usuwanie i aktualizowanie jednostek. Za pomocą EGTs można wsadowo wiele operacji wstawiania, aktualizowania i usuwania, aby zmniejszyć liczbę wymaganych podróży i zwiększyć wydajność rozwiązania.  

Wyjątki zgłaszane, gdy biblioteka klienta magazynu uruchamia EGT zazwyczaj zawiera indeks jednostki, która spowodowała niepowodzenie wykonywania partii. Jest to przydatne w przypadku debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projekt ma wpływ na sposób, w jaki Twoja aplikacja kliencka obsługuje operacje współbieżności i aktualizacji.  

#### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa Table Storage implementuje optymistyczne kontrole współbieżności na poziomie poszczególnych jednostek dla operacji wstawiania, scalania i usuwania, chociaż istnieje możliwość, aby klient wymusił ominięcie tych sprawdzeń przez klienta. Aby uzyskać więcej informacji, zobacz [Zarządzanie współbieżnością w Microsoft Azure Storage](../storage/blobs/concurrency-manage.md).  

#### <a name="merge-or-replace"></a>Scal lub Zamień
`Replace`Metoda `TableOperation` klasy zawsze zastępuje kompletną jednostkę w magazynie tabel. Jeśli nie dołączysz właściwości w żądaniu, gdy ta właściwość istnieje w przechowywanej jednostce, żądanie usunie tę właściwość z przechowywanej jednostki. Jeśli nie chcesz usunąć właściwości jawnie z przechowywanej jednostki, musisz dołączyć każdą właściwość w żądaniu.  

Można użyć `Merge` metody `TableOperation` klasy, aby zmniejszyć ilość danych wysyłanych do magazynu tabel, gdy chcesz zaktualizować jednostkę. `Merge`Metoda zastępuje wszystkie właściwości w przechowywanej jednostce wartościami właściwości z jednostki zawartej w żądaniu. Ta metoda pozostawia nienaruszone wszystkie właściwości w przechowywanej jednostce, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz duże jednostki i potrzebujesz jedynie zaktualizować niewielką liczbę właściwości w żądaniu.  

> [!NOTE]
> `*Replace`Metody i `Merge` kończą się niepowodzeniem, jeśli jednostka nie istnieje. Alternatywnie można użyć `InsertOrReplace` `InsertOrMerge` metod i, które tworzą nową jednostkę, jeśli nie istnieje.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Współpraca z niejednorodnymi typami jednostek
Magazyn tabel jest magazynem tabel bez *schematu* . Oznacza to, że pojedyncza tabela może przechowywać jednostki wielu typów, zapewniając doskonałą elastyczność w projekcie. Poniższy przykład ilustruje tabelę przechowującą jednostki pracownika i działu:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Każda jednostka musi nadal mieć `PartitionKey` `RowKey` wartości,, i `Timestamp` , ale może mieć dowolny zestaw właściwości. Ponadto nie ma nic, aby wskazać typ jednostki, chyba że zdecydujesz się przechowywać te informacje w innym miejscu. Dostępne są dwie opcje identyfikacji typu jednostki:  

* Dołącz typ jednostki do `RowKey` (lub prawdopodobnie `PartitionKey` ). Na przykład `EMPLOYEE_000123` lub `DEPARTMENT_SALES` jako `RowKey` wartości.  
* Użyj oddzielnej właściwości, aby zarejestrować typ jednostki, jak pokazano w poniższej tabeli.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Dział</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Pierwsza opcja, w zależności od typu jednostki `RowKey` , jest przydatna, jeśli istnieje możliwość, że dwie jednostki różnych typów mogą mieć taką samą wartość klucza. Grupuje również jednostki tego samego typu razem w partycji.  

Techniki omówione w tej sekcji są szczególnie istotne dla dyskusji na temat[relacji dziedziczenia](#inheritance-relationships).  

> [!NOTE]
> Rozważ uwzględnienie numeru wersji w wartości typ jednostki, aby umożliwić aplikacjom klienckim rozwijanie obiektów POCO i współpracują z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre funkcje w bibliotece klienta magazynu, które ułatwiają pracę z wieloma typami jednostek w tej samej tabeli.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Pobieranie typów jednostek heterogenicznych
W przypadku korzystania z biblioteki klienta usługi Storage dostępne są trzy opcje pracy z wieloma typami jednostek.  

Jeśli znasz typ jednostki przechowywanej przy użyciu konkretnych `RowKey` i `PartitionKey` wartości, możesz określić typ jednostki podczas pobierania jednostki. Pokazano to w poprzednich dwóch przykładach, które pobierają jednostki typu `EmployeeEntity` : [Uruchom zapytanie punktu przy użyciu biblioteki klienta usługi Storage](#run-a-point-query-by-using-the-storage-client-library) i [Pobierz wiele jednostek przy użyciu LINQ](#retrieve-multiple-entities-by-using-linq).  

Drugą opcją jest użycie `DynamicTableEntity` typu (zbioru właściwości) zamiast konkretnego typu jednostki poco. Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby serializacji i deserializacji jednostki do typów .NET. Poniższy kod w języku C# potencjalnie umożliwia pobranie wielu jednostek różnych typów z tabeli, ale zwraca wszystkie jednostki jako `DynamicTableEntity` wystąpienia. Następnie używa właściwości, `EntityType` Aby określić typ poszczególnych jednostek:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Aby pobrać inne właściwości, należy użyć `TryGetValue` metody we `Properties` właściwości `DynamicTableEntity` klasy.  

Trzecią opcją jest połączenie przy użyciu `DynamicTableEntity` typu i `EntityResolver` wystąpienia. Pozwala to na rozpoznanie wielu typów POCO w tym samym zapytaniu. W tym przykładzie `EntityResolver` Delegat używa `EntityType` właściwości do rozróżniania między dwoma typami obiektów zwracanymi przez zapytanie. `Resolve`Metoda używa `resolver` delegata do rozpoznawania `DynamicTableEntity` wystąpień wystąpienia `TableEntity` .  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>Modyfikowanie typów jednostek heterogenicznych
Nie musisz znać typu jednostki, aby ją usunąć, i zawsze znasz typ jednostki podczas jej wstawiania. Można jednak użyć `DynamicTableEntity` typu, aby zaktualizować jednostkę bez znajomości jej typu i bez użycia klasy jednostki poco. Poniższy przykład kodu pobiera pojedynczą jednostkę i sprawdza, czy `EmployeeCount` Właściwość istnieje przed jej aktualizacją.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>Kontrola dostępu za pomocą sygnatur dostępu współdzielonego
Tokeny sygnatury dostępu współdzielonego (SAS) mogą być używane do włączania aplikacji klienckich do bezpośredniej modyfikacji (i zapytania) jednostek tabeli bez konieczności uwierzytelniania bezpośrednio w usłudze Table Storage. Zazwyczaj istnieją trzy główne korzyści związane z używaniem sygnatury dostępu współdzielonego w aplikacji:  

* Nie jest konieczne dystrybuowanie klucza konta magazynu do niezabezpieczonej platformy (na przykład urządzenia przenośnego) w celu umożliwienia temu urządzeniu dostępu do jednostek w usłudze Table Storage i modyfikowania ich.  
* Można odciążyć część pracy, którą wykonują role sieci Web i procesu roboczego w zarządzaniu jednostkami. Można odciążyć urządzenia klienckie, takie jak komputery użytkowników końcowych i urządzenia przenośne.  
* Do klienta można przypisywać ograniczone i czasochłonne zbiory uprawnień (np. Zezwalanie na dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów SAS z magazynem tabel, zobacz [using Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).  

Należy jednak nadal generować tokeny sygnatury dostępu współdzielonego, które przyznają aplikacji klienckiej jednostki w usłudze Table Storage. Zrób to w środowisku, które ma bezpieczny dostęp do kluczy konta magazynu. Zwykle w celu wygenerowania tokenów SAS i dostarczenia ich do aplikacji klienckich, które wymagają dostępu do jednostek, należy użyć roli sieci Web lub procesu roboczego. Ze względu na to, że nadal występują obciążenia związane z generowaniem i dostarczaniem tokenów SAS klientom, należy wziąć pod uwagę, jak najlepiej zredukować ten koszt, szczególnie w scenariuszach o dużej pojemności.  

Istnieje możliwość wygenerowania tokenu SAS, który udziela dostępu do podzbioru jednostek w tabeli. Domyślnie można utworzyć token sygnatury dostępu współdzielonego dla całej tabeli. Jednak można również określić, że token SAS udziela dostępu do zakresu `PartitionKey` wartości lub zakresu `PartitionKey` i `RowKey` wartości. Można generować tokeny sygnatury dostępu współdzielonego dla poszczególnych użytkowników systemu, tak aby token SAS każdego użytkownika umożliwiał tylko dostęp do ich własnych jednostek w magazynie tabel.  

### <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Pod warunkiem, że są rozłożone żądania na wielu partycjach, można zwiększyć przepływność i czas odpowiedzi klienta przy użyciu zapytań asynchronicznych lub równoległych.
Na przykład może istnieć co najmniej dwa wystąpienia roli procesu roboczego, które uzyskują dostęp do tabel równolegle. Poszczególne role procesów roboczych mogą być odpowiedzialne za określone zestawy partycji lub po prostu mają wiele wystąpień roli procesu roboczego, z których każdy może uzyskać dostęp do wszystkich partycji w tabeli.  

W ramach wystąpienia klienta można zwiększyć przepływność przez wykonywanie operacji magazynu asynchronicznie. Biblioteka klienta magazynu ułatwia pisanie asynchronicznych zapytań i modyfikacji. Na przykład możesz zacząć od metody synchronicznej, która pobiera wszystkie jednostki w partycji, jak pokazano w poniższym kodzie C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Możesz łatwo zmodyfikować ten kod, aby zapytanie zostało uruchomione asynchronicznie w następujący sposób:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

W tym przykładzie asynchronicznym można zobaczyć następujące zmiany w wersji synchronicznej:  

* Podpis metody zawiera teraz `async` modyfikator i zwraca `Task` wystąpienie.  
* Zamiast wywoływania `ExecuteSegmented` metody w celu pobrania wyników metoda wywołuje teraz `ExecuteSegmentedAsync` metodę. Metoda używa `await` modyfikatora do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywoływać tę metodę wiele razy, z różnymi wartościami `department` parametru. Każde zapytanie jest uruchamiane w osobnym wątku.  

Brak asynchronicznej wersji `Execute` metody w `TableQuery` klasie, ponieważ `IEnumerable` interfejs nie obsługuje asynchronicznego wyliczania.  

Można także wstawiać, aktualizować i usuwać jednostki asynchronicznie. W poniższym przykładzie w języku C# przedstawiono prostą, synchroniczną metodę wstawiania lub zastępowania jednostki pracownika:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Możesz łatwo zmodyfikować ten kod, aby aktualizacja działała asynchronicznie, w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchronicznym można zobaczyć następujące zmiany w wersji synchronicznej:  

* Podpis metody zawiera teraz `async` modyfikator i zwraca `Task` wystąpienie.  
* Zamiast wywoływania `Execute` metody w celu zaktualizowania jednostki metoda wywołuje teraz metodę `ExecuteAsync` . Metoda używa `await` modyfikatora do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywoływać wiele metod asynchronicznych, takich jak ta, i każde wywołanie metody jest uruchamiane w osobnym wątku.