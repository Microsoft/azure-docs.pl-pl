---
title: Monitorowanie stanu i wyników indeksatora
titleSuffix: Azure Cognitive Search
description: Monitoruj stan, postęp i wyniki Wyszukiwanie poznawcze indeksatorów platformy Azure w Azure Portal, przy użyciu interfejsu API REST lub zestawu .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 06a31e61583ac28218b34195dd6e5b7f92776dce
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541241"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Jak monitorować stan i wyniki usługi Azure Wyszukiwanie poznawcze indeksator

Usługa Azure Wyszukiwanie poznawcze udostępnia informacje o stanie i monitorowaniu dotyczące bieżących i historycznych przebiegów każdego indeksatora.

Monitorowanie indeksatora jest przydatne, gdy chcesz:

* Śledź postęp indeksatora w trakcie trwającego uruchomienia.
* Przejrzyj wyniki trwającej lub poprzedniej przebiegu indeksatora.
* Zidentyfikuj błędy indeksatora najwyższego poziomu oraz błędy lub ostrzeżenia dotyczące indeksowanych poszczególnych dokumentów.

## <a name="get-status-and-history"></a>Pobierz stan i historię

Informacje o monitorowaniu indeksatora można uzyskać na różne sposoby, w tym:

* W [Azure Portal](#portal)
* Korzystanie z [interfejsu API REST](#restapi)
* Korzystanie z [zestawu SDK platformy .NET](#dotnetsdk)

Dostępne informacje monitorowania indeksatora obejmują wszystkie następujące elementy (chociaż formaty danych różnią się w zależności od używanej metody dostępu):

* Informacje o stanie samego indeksatora
* Informacje na temat ostatniego przebiegu indeksatora, w tym jego stanu, godziny rozpoczęcia i zakończenia oraz szczegóły błędów i ostrzeżeń.
* Lista przebiegów historycznego indeksatora oraz ich stan, wyniki, błędy i ostrzeżenia.

Uruchamianie indeksatorów przetwarzających duże ilości danych może zająć dużo czasu. Na przykład indeksatory obsługujące miliony dokumentów źródłowych mogą być uruchamiane przez 24 godziny, a następnie ponownie uruchomione niemal od razu. Stan indeksatorów wysokiego woluminu może zawsze być **w toku** w portalu. Nawet gdy indeksator jest uruchomiony, dostępne są szczegóły dotyczące ciągłego postępu i poprzednich przebiegów.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Monitorowanie przy użyciu portalu

Bieżący stan wszystkich indeksatorów znajduje się na liście **indeksatorów** na stronie Przegląd usługi wyszukiwania.

   ![Lista indeksatorów](media/search-monitor-indexers/indexers-list.png "Lista indeksatorów")

Gdy indeksator jest wykonywany, stan na liście jest **w toku**, a wartość w polu **Dokumentacja zakończona pomyślnie** pokazuje liczbę dokumentów przetworzonych do tej pory. Aktualizacja wartości stanu indeksatora i liczby dokumentów przez portal może potrwać kilka minut.

Indeksator, którego ostatnie uruchomienie zakończyło się pomyślnie, pokazuje **powodzenie**. Uruchomienie indeksatora może się powieść, nawet jeśli pojedyncze dokumenty mają Błędy, jeśli liczba błędów jest mniejsza niż wartość ustawienia **Maksymalny element elementów zakończonych niepowodzeniem** indeksatora.

Jeśli ostatnie uruchomienie zakończyło się błędem, stan wyświetli **się niepowodzeniem**. Stan **resetowania** oznacza, że stan śledzenia zmian indeksatora został zresetowany.

Kliknij indeksator na liście, aby zobaczyć więcej szczegółów na temat bieżących i ostatnich przebiegów indeksatora.

   ![Historia indeksatora i historię wykonywania](media/search-monitor-indexers/indexer-summary.png "Historia indeksatora i historię wykonywania")

Na wykresie **podsumowania indeksatora** jest wyświetlany wykres liczby dokumentów przetworzonych w ostatnich uruchomieniach.

Lista **szczegóły wykonania** przedstawia do 50 najnowszych wyników wykonania.

Kliknij wynik wykonania na liście, aby zobaczyć szczegóły dotyczące tego uruchomienia. Obejmuje to czasy rozpoczęcia i zakończenia oraz wszelkie błędy i ostrzeżenia, które wystąpiły.

   ![Szczegóły wykonywania indeksatora](media/search-monitor-indexers/indexer-execution.png "Szczegóły wykonywania indeksatora")

Jeśli podczas przebiegu wystąpiły problemy specyficzne dla dokumentu, zostaną one wyświetlone w polach błędy i ostrzeżenia.

   ![Szczegóły indeksatora z błędami](media/search-monitor-indexers/indexer-execution-error.png "Szczegóły indeksatora z błędami")

Ostrzeżenia są wspólne dla niektórych typów indeksatorów i nie zawsze wskazują problem. Na przykład indeksatory korzystające z usług poznawczych mogą raportować ostrzeżenia, gdy pliki obrazów lub PDF nie zawierają żadnego tekstu do przetworzenia.

Aby uzyskać więcej informacji na temat badania błędów i ostrzeżeń indeksatora, zobacz [Rozwiązywanie typowych problemów indeksatora na platformie Azure wyszukiwanie poznawcze](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitorowanie przy użyciu interfejsów API REST

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

Każdy przebieg indeksatora ma również swój własny stan, który wskazuje, czy określone wykonanie jest**wykonywane (uruchomione**), czy już zakończone **powodzeniem**, **transientFailure**lub **persistentFailure** . 

Gdy indeksator zostanie zresetowany w celu odświeżenia stanu śledzenia zmian, zostanie dodany oddzielny wpis historii wykonania ze stanem **Reset** .

Aby uzyskać więcej informacji na temat kodów stanu i danych monitorowania indeksatora, zobacz [GetIndexerStatus](/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitorowanie przy użyciu zestawu .NET SDK

Można zdefiniować harmonogram dla indeksatora przy użyciu zestawu Azure Wyszukiwanie poznawcze .NET SDK. W tym celu należy uwzględnić Właściwość **Schedule** podczas tworzenia lub aktualizowania indeksatora.

Poniższy przykład kodu w języku C# zapisuje informacje o stanie indeksatora i wyniki jego najnowszej (lub trwającej) przebiegu w konsoli programu.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
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
  StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Należy pamiętać, że istnieją dwie różne wartości stanu. Stan najwyższego poziomu to stan samego indeksatora. Stan indeksatora **oznacza,** że indeksator jest prawidłowo skonfigurowany i dostępny do wykonania, ale nie jest aktualnie wykonywany.

Każdy przebieg indeksatora ma również swój własny stan dla tego, czy określone wykonanie jest w toku (**uruchomione**) czy zostało już wykonane z użyciem stanu **sukcesu** lub **TransientError** . 

Gdy indeksator zostanie zresetowany w celu odświeżenia stanu śledzenia zmian, zostanie dodany oddzielny wpis historii ze stanem **Reset** .

Aby uzyskać więcej informacji na temat kodów stanu i informacji dotyczących monitorowania indeksatora, zobacz [GetIndexerStatus](/rest/api/searchservice/get-indexer-status) w interfejsie API REST.

Szczegółowe informacje o błędach lub ostrzeżeniach specyficznych dla dokumentu można pobrać przez Wyliczenie list `IndexerExecutionResult.Errors` i `IndexerExecutionResult.Warnings` .

Aby uzyskać więcej informacji na temat klas zestawu .NET SDK służących do monitorowania indeksatorów, zobacz [IndexerExecutionInfo](/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo) i [IndexerExecutionResult](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult).