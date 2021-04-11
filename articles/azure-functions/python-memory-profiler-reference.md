---
title: Profilowanie pamięci w aplikacjach w języku Python w Azure Functions
description: Dowiedz się, jak profilować użycie pamięci przez aplikacje Python i zidentyfikować wąskie gardło pamięci.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: 26f9040016f9eae7e82a85c589d673c90e542f47
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060316"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profilowanie użycia pamięci przez aplikacje Python w Azure Functions

Podczas programowania lub po wdrożeniu lokalnego projektu aplikacji funkcji w języku Python na platformie Azure dobrym sposobem jest przeanalizowanie potencjalnych wąskich gardeł w zakresie pamięci w funkcjach. Takie wąskie gardła mogą zmniejszyć wydajność funkcji i prowadzić do błędów. Poniższa instrukcja pokazuje, jak używać pakietu Python [Memory-Profiler](https://pypi.org/project/memory-profiler) , który zapewnia analizę zużycia pamięci przez funkcję w miarę ich wykonywania.

> [!NOTE]
> Profilowanie pamięci jest przeznaczone tylko dla analizy zużycia pamięci w środowisku deweloperskim. Nie stosuj profilera pamięci w aplikacjach funkcji produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tworzenia aplikacji funkcji języka Python należy spełnić następujące wymagania:

* [Python 3.6. x lub nowszy](https://www.python.org/downloads/release/python-374/). Aby zapoznać się z pełną listą obsługiwanych wersji języka Python w Azure Functions, przejdź do [przewodnika dla deweloperów języka Python](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2) w wersji 3. x.

* [Visual Studio Code](https://code.visualstudio.com/) zainstalowane na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktywna subskrypcja platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Proces profilowania pamięci

1. W requirements.txt Dodaj, `memory-profiler` Aby upewnić się, że pakiet zostanie powiązany z wdrożeniem. W przypadku tworzenia aplikacji na komputerze lokalnym warto [aktywować środowisko wirtualne języka Python](create-first-function-cli-python.md#create-venv) i przeprowadzić rozpoznawanie pakietów przez program `pip install -r requirements.txt` .

2. W skrypcie funkcji (zwykle \_ \_ init \_ \_ . PR) Dodaj poniższe wiersze powyżej `main()` funkcji. Dzięki temu Rejestrator główny będzie zgłaszał nazwy rejestratora podrzędnego, tak aby dzienniki profilowania pamięci można było rozróżnić prefiksem `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=memory_logger)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Przykład

Poniżej przedstawiono przykład profilowania pamięci na asynchroniczne i synchroniczne wyzwalacze HTTP o nazwie "HttpTriggerAsync" i "HttpTriggerSync". Utworzymy aplikację funkcji języka Python, która po prostu wyśle żądania GET do strony głównej firmy Microsoft.

### <a name="create-a-python-function-app"></a>Tworzenie aplikacji funkcji w języku Python

Aplikacja funkcji języka Python powinna następować Azure Functions określonej [strukturze folderów](functions-reference-python.md#folder-structure). Aby można było połączyć się z projektem, zalecamy użycie Azure Functions Core Tools, uruchamiając następujące polecenia:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Aktualizuj zawartość pliku

requirements.txt definiuje pakiety, które będą używane w naszym projekcie. Oprócz Azure Functions SDK i profilera pamięci wprowadzamy `aiohttp` asynchroniczne żądania HTTP i `requests` synchroniczne wywołania http.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Musimy również ponownie napisać asynchroniczny wyzwalacz HTTP `HttpTriggerAsync/__init__.py` i skonfigurować obiekt profiler pamięci, format rejestratora głównego oraz powiązanie przesyłania strumieniowego rejestratora.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

W przypadku synchronicznego wyzwalacza HTTP zapoznaj się z następującą `HttpTriggerSync/__init__.py` sekcją kodu:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profilowanie aplikacji funkcji w języku Python w lokalnym środowisku programistycznym

Po wprowadzeniu wszystkich powyższych zmian należy wykonać kilka kroków, aby zainicjować wirtualną środowiska środowiska uruchomieniowego Azure Functions w języku Python.

1. Otwórz program Windows PowerShell lub dowolną powłokę systemu Linux zgodnie z preferencjami.
2. Utwórz środowisko wirtualne języka Python `py -m venv .venv` w systemie Windows lub `python3 -m venv .venv` Linux.
3. Aktywuj środowisko wirtualnych języka Python za pomocą programu `.venv\Scripts\Activate.ps1` w programie Windows PowerShell lub `source .venv/bin/activate` w powłoce systemu Linux.
4. Przywróć zależności języka Python za pomocą `pip install requirements.txt`
5. Uruchom środowisko uruchomieniowe Azure Functions lokalnie za pomocą Azure Functions Core Tools `func host start`
6. Wyślij żądanie GET do `https://localhost:7071/api/HttpTriggerAsync` lub `https://localhost:7071/api/HttpTriggerSync` .
7. Powinien on zawierać raport profilowania pamięci podobny do poniższego sekcja w Azure Functions Core Tools.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programowania w języku Python Azure Functions, zobacz następujące zasoby:

* [Przewodnik dewelopera w języku Python Azure Functions](functions-reference-python.md)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
