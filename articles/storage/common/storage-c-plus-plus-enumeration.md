---
title: Wyświetlanie listy zasobów usługi Azure Storage za pomocą biblioteki klienckiej języka C++
description: Dowiedz się, jak wyliczyć kontenery, obiekty blob, kolejki, tabele i jednostki za pomocą interfejsów API listy w Microsoft Azure Storageej bibliotece klienta języka C++.
author: twooley
ms.author: twooley
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 72b08a79ead90207c48c4fa682b708a0627b5df3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279633"
---
# <a name="list-azure-storage-resources-in-c"></a>Generowanie listy zasobów usługi Azure Storage w języku C++

Tworzenie list operacji jest kluczem do wielu scenariuszy programistycznych w usłudze Azure Storage. W tym artykule opisano sposób najbardziej wydajnego wyliczania obiektów w usłudze Azure Storage przy użyciu listy interfejsów API, które znajdują się w bibliotece klienta Microsoft Azure Storage dla języka C++.

> [!NOTE]
> Ten przewodnik dotyczy biblioteki klienta usługi Azure Storage dla języka C++ w wersji 2. x, który jest dostępny za pośrednictwem programu [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp).

Biblioteka klienta usługi Storage zawiera wiele metod tworzenia listy obiektów lub wykonywania względem nich zapytań w usłudze Azure Storage. Ten artykuł dotyczy następujących scenariuszy:

* Wyświetlanie listy kontenerów na koncie
* Wyświetlanie listy obiektów BLOB w kontenerze lub wirtualnym katalogu obiektów BLOB
* Wyświetlanie listy kolejek na koncie
* Wyświetlanie listy tabel na koncie
* Zapytanie jednostek w tabeli

Każda z tych metod jest pokazywana przy użyciu różnych przeciążeń dla różnych scenariuszy.

## <a name="asynchronous-versus-synchronous"></a>Asynchroniczna a synchroniczna

Ponieważ Biblioteka klienta usługi Storage dla języka C++ jest oparta na [bibliotece REST języka c++](https://github.com/Microsoft/cpprestsdk), nie obsługujemy operacji asynchronicznych za pomocą programu [PPLX:: Task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Na przykład:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operacje synchroniczne zawijają odpowiednie operacje asynchroniczne:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Jeśli pracujesz z wieloma aplikacjami lub usługami wątkowości, zalecamy bezpośrednie używanie asynchronicznych interfejsów API zamiast tworzenia wątku do wywoływania interfejsów API synchronizacji, co znacznie wpływa na wydajność.

## <a name="segmented-listing"></a>Lista segmentów

Skala magazynu w chmurze wymaga wyświetlania segmentów. Na przykład możesz mieć ponad milion obiektów BLOB w kontenerze obiektów blob platformy Azure lub za pośrednictwem miliarda jednostek w tabeli platformy Azure. Nie są to liczby teoretyczne, ale prawdziwe przypadki użycia klientów.

W związku z tym nie można wyświetlić listy wszystkich obiektów w pojedynczej odpowiedzi. Zamiast tego można wyświetlić listę obiektów przy użyciu stronicowania. Każdy z interfejsów API zawiera *segmenty* przeciążenia.

Odpowiedź na operację tworzenia segmentów z segmentacją obejmuje:

* *_segment*, który zawiera zestaw wyników zwróconych dla pojedynczego wywołania interfejsu API wyświetlania.
* *continuation_token*, która jest przenoszona do następnego wywołania w celu uzyskania następnej strony wyników. Gdy nie ma więcej wyników do zwrócenia, token kontynuacji ma wartość null.

Na przykład typowe wywołanie listy wszystkich obiektów BLOB w kontenerze może wyglądać podobnie jak w poniższym fragmencie kodu. Kod jest dostępny w naszych [przykładach](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Należy zauważyć, że liczba wyników zwróconych na stronie może być kontrolowana przez parametr *max_results* w przypadku przeciążenia poszczególnych interfejsów API, na przykład:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Jeśli parametr *max_results* nie zostanie określony, domyślna wartość maksymalna do 5000 wyników zostanie zwrócona na jednej stronie.

Należy również zauważyć, że zapytanie w usłudze Azure Table Storage może zwracać Brak rekordów lub mniejszą liczbę rekordów niż wartość parametru *max_results* określonego przez użytkownika, nawet jeśli token kontynuacji nie jest pusty. Jedną z przyczyn może być to, że zapytanie nie zostało wykonane w ciągu pięciu sekund. Tak długo, jak token kontynuacji nie jest pusty, zapytanie powinno być kontynuowane, a Twój kod nie powinien przyjąć rozmiaru wyników segmentu.

Zalecany wzorzec kodowania dla większości scenariuszy to lista segmentów, która zapewnia jawny postęp tworzenia listy lub wykonywania zapytań oraz jak usługa odpowiada na każde żądanie. Szczególnie w przypadku aplikacji lub usług C++ kontrola nad postępem działania może pomóc w kontroli ilości pamięci i wydajności.

## <a name="greedy-listing"></a>Lista zachłanne

Wcześniejsze wersje biblioteki klienta usługi Storage dla języka C++ (wersje 0.5.0 wersja zapoznawcza i starsze) zawierają interfejsy API wyświetlania segmentów dla tabel i kolejek, jak w poniższym przykładzie:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Te metody zostały zaimplementowane jako otoki interfejsów API z segmentacją. Dla każdej odpowiedzi na listę segmentów kod dołącza wyniki do wektora i zwrócił wszystkie wyniki po przeskanowaniu pełnych kontenerów.

Takie podejście może być możliwe, gdy konto magazynu lub tabela zawiera niewielką liczbę obiektów. Jednak ze wzrostem liczby obiektów wymagana ilość pamięci może wzrosnąć bez ograniczenia, ponieważ wszystkie wyniki pozostawały w pamięci. Jedna operacja tworzenia listy może zająć bardzo dużo czasu, podczas gdy obiekt wywołujący nie miał informacji o postępie.

Te zachłanne listy interfejsów API w zestawie SDK nie istnieją w środowisku C#, Java ani JavaScript Node.js. Aby uniknąć potencjalnych problemów z korzystaniem z tych interfejsów API zachłanne, firma Microsoft usunęła je w wersji zapoznawczej 0.6.0.

Jeśli kod wywołuje te interfejsy API zachłanne:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Następnie należy zmodyfikować kod, aby używać interfejsów API z segmentami:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Określając parametr *max_results* segmentu, można zrównoważyć liczbę żądań i użycie pamięci, aby spełnić zagadnienia dotyczące wydajności aplikacji.

Ponadto, jeśli korzystasz z interfejsów API z segmentacją, ale przechowujesz dane w kolekcji lokalnej w stylu "zachłanne", zdecydowanie zalecamy również ponowne użycie kodu do obsługi przechowywania danych w lokalnej kolekcji.

## <a name="lazy-listing"></a>Lista z opóźnieniem

Mimo że lista zachłanne zgłosiła potencjalne problemy, jest to wygodne, jeśli w kontenerze nie ma zbyt wielu obiektów.

Jeśli używasz również zestawów SDK języka C# lub Oracle Java, należy zapoznać się z wyliczalnym modelem programowania, który oferuje listę w stylu z opóźnieniem, w której dane z określonego przesunięcia są pobierane tylko wtedy, gdy jest to wymagane. W języku C++, szablon oparty na iteratorze zapewnia również podobne podejście.

Typowy interfejs API aukcji z opóźnieniem, przy użyciu **list_blobs** na przykład wygląda następująco:

```cpp
list_blob_item_iterator list_blobs() const;
```

Typowy fragment kodu, który używa wzorca aukcji z opóźnieniem, może wyglądać następująco:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Należy zauważyć, że lista z opóźnieniem jest dostępna tylko w trybie synchronicznym.

W porównaniu z listą zachłanne, lista z opóźnieniem pobiera dane tylko w razie potrzeby. W obszarze okładek pobiera dane z usługi Azure Storage tylko wtedy, gdy następny iterator przechodzi do następnego segmentu. W związku z tym użycie pamięci jest kontrolowane z ograniczonym rozmiarem, a operacja jest szybka.

Interfejsy API listy z opóźnieniem są zawarte w bibliotece klienta usługi Storage dla języka C++ w wersji 2.2.0.

## <a name="conclusion"></a>Podsumowanie

W tym artykule omówiono różne przeciążenia dotyczące wyświetlania listy interfejsów API dla różnych obiektów w bibliotece klienta usługi Storage dla języka C++. Podsumowując:

* Asynchroniczne interfejsy API są zdecydowanie zalecane w scenariuszach obejmujących wiele wątków.
* Lista segmentów jest zalecana w przypadku większości scenariuszy.
* Lista z opóźnieniem jest udostępniana w bibliotece jako wygodna otoka w scenariuszach synchronicznych.
* Lista zachłanne nie jest zalecana i została usunięta z biblioteki.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Storage i biblioteki klienckiej dla języka C++, zobacz następujące zasoby.

* [Jak używać Blob Storage z C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Jak używać Table Storage z C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Jak używać usługi Queue Storage z poziomu języka C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
* [Biblioteka klienta usługi Azure Storage dla interfejsu API języka C++.](https://azure.github.io/azure-storage-cpp/)
* [Blog zespołu usługi Azure Storage](/archive/blogs/windowsazurestorage/)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)