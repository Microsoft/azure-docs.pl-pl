---
title: Projektowanie skalowalnych i wydajnych tabel w usłudze Azure Table Storage. | Microsoft Docs
description: Dowiedz się, jak projektować skalowalne i wydajne tabele w usłudze Azure Table Storage. Przejrzyj partycje tabeli, transakcje grupy jednostek oraz informacje o pojemności i kosztach.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 8f3bd2a998066804bfb589e3262ac5e68db601fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306940"
---
# <a name="design-scalable-and-performant-tables"></a>Projektowanie skalowalnych i wydajnych tabel

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Aby zaprojektować skalowalne i wydajne tabele, należy wziąć pod uwagę czynniki takie jak wydajność, skalowalność i koszt. Jeśli wcześniej zaprojektowano schematy dla relacyjnych baz danych, te zagadnienia są znane, ale chociaż istnieją różne podobieństwa między modelem magazynu Table service platformy Azure i modeli relacyjnych, istnieją także istotne różnice. Te różnice zwykle prowadzą do różnych projektów, które mogą odszukać, że są one intuicyjne lub niewłaściwe dla kogoś, kto zna relacyjne bazy danych, ale warto mieć sens, jeśli projektujesz dla magazynu NoSQL klucz/wartość, takiego jak Table service platformy Azure. Wiele z różnic w projekcie odzwierciedla fakt, że Table service jest zaprojektowana do obsługi aplikacji w skali chmury, które mogą zawierać miliardy jednostek (lub wierszy w terminologii relacyjnej bazy danych) lub zestawy danych, które muszą obsługiwać duże ilości transakcji. W związku z tym należy myśleć o sposobie przechowywania danych i zrozumieć, jak działa Table service. Dobrze zaprojektowany magazyn danych NoSQL może pozwalać na skalowanie rozwiązania znacznie bardziej wydajnych i niższych kosztów niż rozwiązanie korzystające z relacyjnej bazy danych. Ten przewodnik pomoże Ci w zapewnieniu tego tematu.  

## <a name="about-the-azure-table-service"></a>Informacje o Table service platformy Azure
W tej sekcji omówiono niektóre kluczowe funkcje Table service, które są szczególnie przydatne podczas projektowania pod kątem wydajności i skalowalności. Jeśli dopiero zaczynasz korzystać z usługi Azure Storage i Table service, najpierw Przeczytaj [wprowadzenie do Microsoft Azure Storage](../../storage/common/storage-introduction.md) i [Rozpocznij pracę z platformą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) przed przeczytaniem pozostałej części tego artykułu. Chociaż ten przewodnik znajduje się na Table service, zawiera omówienie usługi Azure Queue i BLOB Services oraz sposób ich używania z Table service.  

Co to jest Table service? Zgodnie z oczekiwaniami, Table service używa formatu tabelarycznego do przechowywania danych. W standardowej terminologii każdy wiersz tabeli reprezentuje jednostkę, a kolumny przechowują różne właściwości tej jednostki. Każda jednostka ma parę kluczy do unikatowego identyfikowania oraz kolumnę sygnatury czasowej używaną przez Table service do śledzenia czasu ostatniej aktualizacji jednostki. Sygnatura czasowa jest stosowana automatycznie i nie można ręcznie nadpisać sygnatury czasowej z dowolną wartością. Table service używa tego ostatniej modyfikacji sygnatury czasowej (LMT) w celu zarządzania optymistyczną współbieżność.  

> [!NOTE]
> Operacje interfejsu API REST Table service zwracają również wartość **ETag** , która pochodzi od LMT. Ten dokument używa elementów ETag i LMT zamiennie, ponieważ odwołują się do tych samych danych źródłowych.  
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


Do tej pory te dane wyglądają podobnie do tabeli w relacyjnej bazie danych, a kluczowe różnice są obowiązkowymi kolumnami i możliwość przechowywania wielu typów jednostek w tej samej tabeli. Ponadto każda Właściwość zdefiniowana przez użytkownika, taka jak **FirstName** lub **Age** ma typ danych, na przykład liczba całkowita lub ciąg, podobnie jak kolumna w relacyjnej bazie danych. Chociaż w przeciwieństwie do relacyjnej bazy danych, charakter bez schematu Table service oznacza, że właściwość nie musi mieć tego samego typu danych dla każdej jednostki. Aby przechowywać złożone typy danych w pojedynczej właściwości, należy użyć serializowanego formatu, takiego jak JSON lub XML. Aby uzyskać więcej informacji na temat usługi Table Service, takiej jak obsługiwane typy danych, obsługiwane zakresy dat, reguły nazewnictwa i ograniczenia rozmiaru, zobacz [Omówienie modelu danych usługi Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Wybór **PartitionKey** i **RowKey** ma podstawowe znaczenie dla dobrego projektu tabeli. Każda jednostka przechowywana w tabeli musi mieć unikatową kombinację **PartitionKey** i **RowKey**. Podobnie jak w przypadku kluczy w tabeli relacyjnej bazy danych, wartości **PartitionKey** i **RowKey** są indeksowane w celu utworzenia indeksu klastrowanego umożliwiającego szybkie wyszukiwanie. Jednak Table service nie tworzy żadnych indeksów pomocniczych, więc **PartitionKey** i **RowKey** są jedynymi właściwościami indeksowanymi. Niektóre wzorce opisane w [wzorcach projektowych tabeli](table-storage-design-patterns.md) ilustrują, jak można obejść to oczywiste ograniczenie.  

Tabela składa się z jednej lub kilku partycji, a wiele z podjętych decyzji projektowych będzie wokół wyboru odpowiednich **PartitionKey** i **RowKey** w celu zoptymalizowania rozwiązania. Rozwiązanie może składać się z pojedynczej tabeli, która zawiera wszystkie jednostki zorganizowane w partycje, ale zazwyczaj rozwiązanie ma wiele tabel. Tabele ułatwiają logiczne organizowanie jednostek, ułatwiają zarządzanie dostępem do danych przy użyciu list kontroli dostępu, a także usuwanie całej tabeli przy użyciu jednej operacji magazynu.  

## <a name="table-partitions"></a>Partycje tabeli
Nazwa konta, nazwa tabeli i **PartitionKey** wspólnie identyfikują partycję w ramach usługi magazynu, w której usługa Table przechowuje jednostkę. Jak również część schematu adresowania dla jednostek, partycje definiują zakres dla transakcji (zobacz [transakcje grupy jednostek](#entity-group-transactions) poniżej) i stanowią podstawę skalowania usługi Table Service. Aby uzyskać więcej informacji o partycjach, zobacz [Lista kontrolna wydajności i skalowalności dla usługi Table Storage](storage-performance-checklist.md).  

W Table service poszczególne usługi węzłów mają co najmniej jedną kompletną partycję, a usługa jest skalowana przez dynamiczne Równoważenie obciążenia partycji między węzłami. Jeśli węzeł jest w obciążeniu, usługa Table Service może *podzielić* zakres partycji objętych przez ten węzeł na różne węzły; Po nawrocie ruchu usługa może *scalić* zakresy partycji z cichych węzłów z powrotem do jednego węzła.  

Aby uzyskać więcej informacji o wewnętrznych szczegółach Table service, a w szczególności o tym, jak usługa zarządza partycjami, zobacz dokument [Microsoft Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

## <a name="entity-group-transactions"></a>Transakcje grupy jednostek
W Table service, transakcje grupy jednostek (EGTs) są jedynym wbudowanym mechanizmem do wykonywania niepodzielnych aktualizacji w wielu jednostkach. EGTs są czasami nazywane również *transakcjami wsadowymi*. EGTs może działać tylko na jednostkach przechowywanych w tej samej partycji (czyli udostępnić ten sam klucz partycji w danej tabeli). Aby w dowolnym momencie wymagać niepodzielnych zachowań transakcyjnych w wielu jednostkach, należy się upewnić, że te jednostki znajdują się w tej samej partycji. Jest to często powód, aby zachować wiele typów jednostek w tej samej tabeli (i partycji), a nie używać wielu tabel dla różnych typów jednostek. Pojedynczy EGT może działać na maksymalnie 100 jednostkach.  Jeśli przesyłasz wiele współbieżnych EGTs do przetwarzania, należy upewnić się, że te EGTs nie działają na jednostkach wspólnych dla EGTs; w przeciwnym razie przetwarzanie może być opóźnione.

EGTs również wprowadza potencjalne rozwiązanie do szacowania w projekcie. Oznacza to, że użycie większej liczby partycji zwiększa skalowalność aplikacji, ponieważ platforma Azure ma więcej możliwości w przypadku żądań równoważenia obciążenia między węzłami. Jednak użycie większej liczby partycji może ograniczyć możliwość wykonywania przez aplikacje niepodzielnych transakcji i zapewnienia silnej spójności danych. Ponadto istnieją konkretne cele skalowalności na poziomie partycji, która może ograniczyć przepływność transakcji, które można oczekiwać dla jednego węzła. Aby uzyskać więcej informacji na temat celów skalowalności dla kont magazynu w warstwie Standardowa, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md). Aby uzyskać więcej informacji dotyczących skalowalności Table service, zobacz [cele skalowalności i wydajności dla usługi Table Storage](scalability-targets.md).

## <a name="capacity-considerations"></a>Zagadnienia dotyczące pojemności

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kwestie związane z kosztami
Magazyn tabel jest stosunkowo niedrogi, ale należy uwzględnić oszacowania kosztów zarówno w przypadku użycia pojemności, jak i liczby transakcji w ramach oceny dowolnego Table service rozwiązania. Jednak w wielu scenariuszach przechowywanie nieznormalizowanych lub zduplikowanych danych w celu poprawy wydajności lub skalowalności rozwiązania jest prawidłowym podejściem. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabeli](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)