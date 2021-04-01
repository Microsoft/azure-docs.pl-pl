---
title: Scenariusze dotyczące wentylatorów/wentylatorów w Durable Functions na platformie Azure
description: Dowiedz się, jak wdrożyć w Durable Functions rozszerzenia wentylator-out-wentylator w Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 91128033696af6a56488db7991987f1e384b719e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027660"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scenariusz na zewnątrz/wentylator w Durable Functions — przykład kopii zapasowej w chmurze

Wentylator */wentylator — w* odniesieniu do wzorca wykonywania wielu funkcji współbieżnie, a następnie wykonywania pewnej agregacji wyników. W tym artykule opisano przykład, który używa [Durable Functions](durable-functions-overview.md) do implementowania scenariusza wentylator-in/out. Przykładem jest funkcja trwała, która tworzy kopię zapasową całej lub pewnej zawartości witryny w usłudze Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

W tym przykładzie funkcja przekaże wszystkie pliki w określonym katalogu cyklicznie do magazynu obiektów BLOB. Liczą również łączną liczbę przekazanych bajtów.

Istnieje możliwość napisania pojedynczej funkcji, która zajmuje się wszystkimi elementami. Główną przyczyną problemu jest **skalowalność**. Pojedyncze wykonanie funkcji można uruchomić tylko na jednej maszynie wirtualnej, dzięki czemu przepływność będzie ograniczać przepływność tej pojedynczej maszyny wirtualnej. Innym problemem jest **niezawodność**. Jeśli wystąpi awaria w połowie, lub jeśli cały proces trwa dłużej niż 5 minut, tworzenie kopii zapasowej może zakończyć się niepowodzeniem w stanie częściowo zakończonym. Następnie należy ponownie uruchomić.

Bardziej niezawodną metodą jest zapisanie dwóch funkcji regularnych: jedna z nich spowoduje Wyliczenie plików i dodanie nazw plików do kolejki, a druga odczytana z kolejki i przekazanie plików do usługi BLOB Storage. Ta metoda jest lepsza pod względem przepływności i niezawodności, ale wymaga aprowizacji i zarządzania kolejką. Co ważniejsze, znacząca złożoność jest wprowadzana w zakresie **zarządzania Stanami** i **koordynacji** , jeśli chcesz zrobić coś więcej, na przykład Zgłoś łączną liczbę przekazanych bajtów.

Podejście Durable Functions zapewnia wszystkie wymienione korzyści z bardzo niskimi kosztami.

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E2_BackupSiteContent`: [Funkcja programu Orchestrator](durable-functions-bindings.md#orchestration-trigger) , która wywołuje program `E2_GetFileList` , aby uzyskać listę plików do utworzenia kopii zapasowej, a następnie wywołuje `E2_CopyFileToBlob` kopię zapasową każdego pliku.
* `E2_GetFileList`: [Funkcja działania](durable-functions-bindings.md#activity-trigger) zwracająca listę plików w katalogu.
* `E2_CopyFileToBlob`: Funkcja działania, która tworzy kopię zapasową pojedynczego pliku na platformie Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent funkcja programu Orchestrator

Ta funkcja programu Orchestrator wykonuje następujące czynności:

1. Przyjmuje `rootDirectory` wartość jako parametr wejściowy.
2. Wywołuje funkcję, aby uzyskać cykliczną listę plików w `rootDirectory` .
3. Wykonuje wiele wywołań funkcji równoległych, aby przekazać każdy plik do Blob Storage platformy Azure.
4. Czeka na zakończenie wszystkich operacji przekazywania.
5. Zwraca łączną sumę bajtów przesłanych do usługi Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Oto kod implementujący funkcję programu Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Zwróć uwagę na `await Task.WhenAll(tasks);` wiersz. Wszystkie poszczególne wywołania `E2_CopyFileToBlob` funkcji *nie* zostały oczekiwane, co umożliwia ich równoległe uruchamianie. Gdy przeniesiemy tę tablicę zadań do `Task.WhenAll` , uzyskamy zadanie, które nie zostanie ukończone, *dopóki nie zostaną ukończone wszystkie operacje kopiowania*. Jeśli znasz bibliotekę zadań równoległych (TPL) w programie .NET, to nie jest to nowa. Różnica polega na tym, że te zadania mogą być uruchamiane na wielu maszynach wirtualnych współbieżnie, a rozszerzenie Durable Functions zapewnia odporność na zakończenie procesu odtwarzania.

Po oczekiwaniu od `Task.WhenAll` , wiemy, że wszystkie wywołania funkcji zostały ukończone i z powrotem zwracają wartości. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę przekazanych bajtów, więc obliczenie łącznej liczby bajtów sum jest kwestią dodawania wszystkich zwracanych wartości.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funkcja używa standardowej *function.jsna* potrzeby funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Oto kod implementujący funkcję programu Orchestrator:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Zwróć uwagę na `yield context.df.Task.all(tasks);` wiersz. Wszystkie poszczególne wywołania `E2_CopyFileToBlob` funkcji *nie były obsługiwane* , co umożliwia ich równoległe uruchamianie. Gdy przeniesiemy tę tablicę zadań do `context.df.Task.all` , uzyskamy zadanie, które nie zostanie ukończone, *dopóki nie zostaną ukończone wszystkie operacje kopiowania*. Jeśli znasz [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) język JavaScript, to nie jest to możliwe. Różnica polega na tym, że te zadania mogą być uruchamiane na wielu maszynach wirtualnych współbieżnie, a rozszerzenie Durable Functions zapewnia odporność na zakończenie procesu odtwarzania.

> [!NOTE]
> Chociaż zadania są koncepcyjnie podobne do języka JavaScript niesie obietnice zwiększenia, należy używać funkcji `context.df.Task.all` programu Orchestrator i `context.df.Task.any` zamiast nich `Promise.all` `Promise.race` zarządzać przetwarzanie równoległe zadań.

Po uzyskaniu z programu `context.df.Task.all` wiemy, że wszystkie wywołania funkcji zostały zakończone i zwrócone wartości są z powrotem. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę przekazanych bajtów, więc obliczenie łącznej liczby bajtów sum jest kwestią dodawania wszystkich zwracanych wartości.

# <a name="python"></a>[Python](#tab/python)

Funkcja używa standardowej *function.jsna* potrzeby funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/function.json)]

Oto kod implementujący funkcję programu Orchestrator:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_BackupSiteContent/\_\_init\_\_.py)]

Zwróć uwagę na `yield context.task_all(tasks);` wiersz. Wszystkie poszczególne wywołania `E2_CopyFileToBlob` funkcji *nie były obsługiwane* , co umożliwia ich równoległe uruchamianie. Gdy przeniesiemy tę tablicę zadań do `context.task_all` , uzyskamy zadanie, które nie zostanie ukończone, *dopóki nie zostaną ukończone wszystkie operacje kopiowania*. Jeśli znasz już program [`asyncio.gather`](https://docs.python.org/3/library/asyncio-task.html#asyncio.gather) w języku Python, to nie jest to możliwe. Różnica polega na tym, że te zadania mogą być uruchamiane na wielu maszynach wirtualnych współbieżnie, a rozszerzenie Durable Functions zapewnia odporność na zakończenie procesu odtwarzania.

> [!NOTE]
> Chociaż zadania są koncepcyjnie podobne do języka Python awaitables, należy używać funkcji `yield` programu Orchestrator `context.task_all` oraz `context.task_any` interfejsów API do zarządzania przetwarzanie równoległe zadań.

Po uzyskaniu z programu `context.task_all` wiemy, że wszystkie wywołania funkcji zostały zakończone i zwrócone wartości są z powrotem. Każde wywołanie `E2_CopyFileToBlob` zwraca liczbę przekazanych bajtów, dzięki czemu można obliczyć łączną liczbę bajtów łącznie, dodając wszystkie zwrócone wartości.

---

### <a name="helper-activity-functions"></a>Funkcje działania pomocnika

Działania pomocnika, podobnie jak inne przykłady, są tylko regularnymi funkcjami, które używają `activityTrigger` powiązania wyzwalacza.

#### <a name="e2_getfilelist-activity-function"></a>Funkcja działania E2_GetFileList

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jsw* pliku dla wygląda następująco `E2_GetFileList` :

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

A oto implementacja:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funkcja używa `readdirp` modułu (wersja 2. x) do rekursywnego odczytywania struktury katalogów.

# <a name="python"></a>[Python](#tab/python)

*function.jsw* pliku dla wygląda następująco `E2_GetFileList` :

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/function.json)]

A oto implementacja:

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_GetFileList/\_\_init\_\_.py)]

---

> [!NOTE]
> Być może zastanawiasz się, dlaczego nie można bezpośrednio umieścić tego kodu w funkcji programu Orchestrator. Może to jednak spowodować uszkodzenie jednej z podstawowych reguł funkcji programu Orchestrator, co oznacza, że nigdy nie powinny wykonywać operacji we/wy, w tym dostępu do lokalnego systemu plików. Aby uzyskać więcej informacji, zobacz [ograniczenia kodu funkcji programu Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>Funkcja działania E2_CopyFileToBlob

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Musisz zainstalować `Microsoft.Azure.WebJobs.Extensions.Storage` pakiet NuGet, aby uruchomić przykładowy kod.

Funkcja używa niektórych zaawansowanych funkcji powiązań Azure Functions (to jest użycie [ `Binder` parametru](../functions-dotnet-class-library.md#binding-at-runtime)), ale nie trzeba martwić się o te szczegóły na potrzeby tego przewodnika.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jsw* pliku dla `E2_CopyFileToBlob` jest podobnie proste:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

Implementacja języka JavaScript używa [zestawu SDK usługi Azure Storage dla węzła](https://github.com/Azure/azure-storage-node) do przekazywania plików do usługi Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

# <a name="python"></a>[Python](#tab/python)

*function.jsw* pliku dla `E2_CopyFileToBlob` jest podobnie proste:

[!code-json[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/function.json)]

Implementacja języka Python używa [zestawu SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python) do przekazywania plików do usługi Azure Blob Storage.

[!code-python[Main](~/samples-durable-functions-python/samples/fan_in_fan_out/E2_CopyFileToBlob/\_\_init\_\_.py)]

---

Implementacja ładuje plik z dysku i asynchronicznie przesyła zawartość do obiektu BLOB o takiej samej nazwie w kontenerze "Backups". Wartość zwracana to liczba bajtów skopiowanych do magazynu, które są następnie używane przez funkcję programu Orchestrator do obliczania sumy zagregowanej.

> [!NOTE]
> Jest to idealny przykład przechodzenia operacji we/wy do `activityTrigger` funkcji. Nie tylko można rozłożyć pracy na wiele różnych maszyn, ale również uzyskać korzyści z przetworzenia punktów kontrolnych postępu. Jeśli proces hosta zostanie zakończony z dowolnego powodu, wiesz, które operacje przekazywania zostały już ukończone.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aranżację można uruchomić w systemie Windows, wysyłając następujące żądanie HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

Alternatywnie, w aplikacja funkcji systemu Linux (język Python jest obecnie uruchamiany tylko w systemie Linux dla App Service), można uruchomić aranżację w następujący sposób:

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"/home/site/wwwroot"
```

> [!NOTE]
> `HttpStart`Funkcja, która jest wywoływana, działa tylko z zawartością sformatowaną w formacie JSON. Z tego powodu `Content-Type: application/json` nagłówek jest wymagany, a ścieżka katalogu jest zaszyfrowana jako ciąg JSON. Ponadto fragment kodu HTTP zakłada, że w pliku znajduje się wpis, `host.json` który usuwa `api/` prefiks domyślny ze wszystkich adresów URL funkcji wyzwalacza http. Znaczniki tej konfiguracji można znaleźć w `host.json` pliku w przykładach.

To żądanie HTTP wyzwala `E2_BackupSiteContent` koordynatora i przekazuje ciąg `D:\home\LogFiles` jako parametr. Odpowiedź zawiera link umożliwiający uzyskanie stanu operacji tworzenia kopii zapasowej:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W zależności od liczby plików dziennika w aplikacji funkcji ta operacja może potrwać kilka minut. Najnowszego stanu można uzyskać, przechodząc do adresu URL w `Location` nagłówku poprzedniej odpowiedzi HTTP 202.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

W takim przypadku funkcja jest nadal uruchomiona. Możesz zobaczyć dane wejściowe, które zostały zapisane w stanie programu Orchestrator i czas ostatniej aktualizacji. Można nadal używać `Location` wartości nagłówka do sondowania w celu ukończenia. Gdy stan to "ukończono", zobaczysz wartość odpowiedzi HTTP podobną do następującej:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Teraz można zobaczyć, że aranżacja została ukończona i około chwilą jej ukończenia. Zobaczysz również wartość `output` pola, która wskazuje, że przekazano około 450 KB dzienników.

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje, jak zaimplementować wzorzec wentylatorów/wentylatorów. Następny przykład pokazuje, jak zaimplementować wzorzec monitora przy użyciu [trwałych czasomierzy](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Uruchamianie przykładowego monitora](durable-functions-monitor.md)