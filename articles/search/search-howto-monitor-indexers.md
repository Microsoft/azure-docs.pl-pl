---
title: Monitorowanie stanu i wyników indeksatora
titleSuffix: Azure Cognitive Search
description: Monitoruj stan, postęp i wyniki Wyszukiwanie poznawcze indeksatorów platformy Azure w Azure Portal, przy użyciu interfejsu API REST lub zestawu .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99063265"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Jak monitorować stan i wyniki usługi Azure Wyszukiwanie poznawcze indeksator

Można monitorować przetwarzanie indeksatora w Azure Portal lub programowo za pomocą wywołań REST lub zestawu Azure SDK. Oprócz stanu samego indeksatora można przejrzeć godziny rozpoczęcia i zakończenia oraz szczegóły błędów i ostrzeżeń z określonego uruchomienia.

## <a name="monitor-using-azure-portal"></a>Monitoruj przy użyciu Azure Portal

Bieżący stan wszystkich indeksatorów można zobaczyć na stronie Przegląd usługi wyszukiwania. Strony portalu odświeżają co kilka minut, więc nie będzie można od razu zobaczyć dowód nowego uruchomienia indeksatora.

   ![Lista indeksatorów](media/search-monitor-indexers/indexers-list.png "Lista indeksatorów")

| Stan | Opis |
|--------|-------------|
| **W toku** | Wskazuje aktywne wykonanie. Portal wyśle raport dotyczący częściowych informacji. W miarę postępów indeksowania można obserwować, że **Dokumentacja została zakończona pomyślnie** . Uruchamianie indeksatorów przetwarzających duże ilości danych może zająć dużo czasu. Na przykład indeksatory obsługujące miliony dokumentów źródłowych mogą być uruchamiane przez 24 godziny, a następnie ponownie uruchomione niemal od razu. Stan indeksatorów wysokiego woluminu może zawsze być **w toku** w portalu. Nawet gdy indeksator jest uruchomiony, dostępne są szczegóły dotyczące ciągłego postępu i poprzednich przebiegów. |
| **Powodzenie** | Wskazuje, że uruchomienie zakończyło się pomyślnie. Uruchomienie indeksatora może się powieść, nawet jeśli pojedyncze dokumenty mają Błędy, jeśli liczba błędów jest mniejsza niż wartość ustawienia **Maksymalny element elementów zakończonych niepowodzeniem** indeksatora. |
| **Niepowodzenie** | Liczba błędów przekroczyła **maksymalną liczbę elementów zakończonych niepowodzeniem** i indeksowanie zostało zatrzymane. |
| **Reset** | Wewnętrzny stan śledzenia zmian indeksatora został zresetowany. Indeksator zostanie uruchomiony w całości, odświeżone wszystkie dokumenty, a nie tylko za pomocą nowszych sygnatur czasowych. |

Możesz kliknąć indeksator na liście, aby zobaczyć więcej szczegółów na temat bieżących i ostatnich przebiegów indeksatora.

   ![Historia indeksatora i historię wykonywania](media/search-monitor-indexers/indexer-summary.png "Historia indeksatora i historię wykonywania")

Na wykresie **podsumowania indeksatora** jest wyświetlany wykres liczby dokumentów przetworzonych w ostatnich uruchomieniach.

Lista **szczegóły wykonania** przedstawia do 50 najnowszych wyników wykonania. Kliknij wynik wykonania na liście, aby zobaczyć szczegóły dotyczące tego uruchomienia. Obejmuje to czasy rozpoczęcia i zakończenia oraz wszelkie błędy i ostrzeżenia, które wystąpiły.

   ![Szczegóły wykonywania indeksatora](media/search-monitor-indexers/indexer-execution.png "Szczegóły wykonywania indeksatora")

Jeśli podczas przebiegu wystąpiły problemy specyficzne dla dokumentu, zostaną one wyświetlone w polach błędy i ostrzeżenia.

   ![Szczegóły indeksatora z błędami](media/search-monitor-indexers/indexer-execution-error.png "Szczegóły indeksatora z błędami")

Ostrzeżenia są wspólne dla niektórych typów indeksatorów i nie zawsze wskazują problem. Na przykład indeksatory korzystające z usług poznawczych mogą raportować ostrzeżenia, gdy pliki obrazów lub PDF nie zawierają żadnego tekstu do przetworzenia. 

Aby uzyskać więcej informacji na temat badania błędów i ostrzeżeń indeksatora, zobacz [Rozwiązywanie typowych problemów indeksatora na platformie Azure wyszukiwanie poznawcze](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Monitorowanie przy użyciu funkcji pobierania indeksatora (interfejs API REST)

Można pobrać stan i historię wykonywania indeksatora przy użyciu [polecenia Pobierz indeksator stanu](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Odpowiedź zawiera ogólny stan indeksatora, ostatnie (lub w toku) wywołanie indeksatora i historię najnowszych wywołań indeksatora.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

Historia wykonywania zawiera maksymalnie 50 ostatnich przebiegów, które są sortowane w odwrotnej kolejności chronologicznej (ostatnio najpierw).

Należy pamiętać, że istnieją dwie różne wartości stanu. Stan najwyższego poziomu jest przeznaczony dla samego indeksatora. Stan indeksatora **oznacza, że indeksator** jest prawidłowo skonfigurowany i dostępny do uruchomienia, ale nie jest aktualnie uruchomiony.

Każdy przebieg indeksatora ma również swój własny stan, który wskazuje, czy określone wykonanie jest **wykonywane (uruchomione**), czy już zakończone **powodzeniem**, **transientFailure** lub **persistentFailure** . 

Gdy indeksator zostanie zresetowany w celu odświeżenia stanu śledzenia zmian, zostanie dodany oddzielny wpis historii wykonania ze stanem **Reset** .

Aby uzyskać więcej informacji na temat kodów stanu i danych monitorowania indeksatora, zobacz [pobieranie stanu indeksatora](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Monitoruj przy użyciu platformy .NET

Korzystając z zestawu SDK platformy Azure Wyszukiwanie poznawcze .NET, Poniższy przykład kodu w języku C# zapisuje informacje o stanie indeksatora i wyniki jego najnowszej (lub trwającej) przebiegu w konsoli programu.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Dane wyjściowe w konsoli będą wyglądać następująco:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Należy pamiętać, że istnieją dwie różne wartości stanu. Stan najwyższego poziomu to stan samego indeksatora. Stan indeksatora **oznacza,** że indeksator jest prawidłowo skonfigurowany i dostępny do wykonania, ale nie jest aktualnie wykonywany.

Każdy przebieg indeksatora ma również swój własny stan dla tego, czy określone wykonanie jest w toku (**uruchomione**) czy zostało już wykonane z użyciem stanu **sukcesu** lub **TransientError** . 

Gdy indeksator zostanie zresetowany w celu odświeżenia stanu śledzenia zmian, zostanie dodany oddzielny wpis historii ze stanem **Reset** .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat kodów stanu i informacji o monitorowaniu indeksatora, zapoznaj się z następującym odwołaniem do interfejsu API:

* [GetIndexerStatus (interfejs API REST)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)