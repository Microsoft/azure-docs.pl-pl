---
title: Zwiększenie wydajności przepływności aplikacji w języku Python w Azure Functions
description: Dowiedz się, jak opracowywać aplikacje Azure Functions przy użyciu języka Python, które są wysoce wydajne i skalowalne pod obciążeniem.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: baa362f029678f266f154df912a9178a6626667d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935873"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Zwiększenie wydajności przepływności aplikacji w języku Python w Azure Functions

Podczas tworzenia dla Azure Functions przy użyciu języka Python należy zrozumieć, w jaki sposób działają funkcje, oraz wpływ tej wydajności na sposób skalowania aplikacji funkcji. Konieczność jest bardziej ważna podczas projektowania wysoce wydajnych aplikacji. Główne czynniki, które należy wziąć pod uwagę podczas projektowania, pisania i konfigurowania aplikacji funkcji, to skalowanie w poziomie i konfiguracja wydajności przepływności.

## <a name="horizontal-scaling"></a>Skalowanie w poziomie
Domyślnie Azure Functions automatycznie monitoruje obciążenie aplikacji i tworzy dodatkowe wystąpienia hosta dla języka Python zgodnie z potrzebami. Azure Functions używa wbudowanych progów dla różnych typów wyzwalaczy, aby określić, kiedy należy dodać wystąpienia, takie jak wiek komunikatów i rozmiar kolejki dla QueueTrigger. Progi te nie są konfigurowane przez użytkownika. Aby uzyskać więcej informacji, zobacz [skalowanie oparte na zdarzeniach w Azure Functions](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Zwiększanie wydajności przepływności

Konfiguracje domyślne są odpowiednie dla większości Azure Functions aplikacji. Można jednak zwiększyć wydajność przepływności aplikacji, używając konfiguracji opartych na Twoim profilu obciążenia. Pierwszym krokiem jest zrozumienie typu obciążenia, które jest uruchomione.

|| Obciążenie związane we/wy | Obciążenie związane z PROCESORem |
|--| -- | -- |
|Charakterystyka aplikacji funkcji| <ul><li>Aplikacja musi obsługiwać wiele współbieżnych wywołań.</li> <li> Aplikacja przetwarza dużą liczbę zdarzeń we/wy, takich jak wywołania sieciowe i odczyt/zapis na dysku.</li> </ul>| <ul><li>Aplikacja wykonuje długotrwałe obliczenia, takie jak zmienianie rozmiarów obrazów.</li> <li>Aplikacja wykonuje transformację danych.</li> </ul> |
|Przykłady| <ul><li>Interfejsy API sieci Web</li><ul> | <ul><li>Przetwarzanie danych</li><li> Wnioskowanie dotyczące uczenia maszynowego</li><ul>|

 
Ponieważ rzeczywiste obciążenia funkcji są zwykle kombinacją operacji we/wy i procesora CPU, należy profilować aplikację w ramach realistycznych obciążeń produkcyjnych.


### <a name="performance-specific-configurations"></a>Konfiguracje specyficzne dla wydajności

Po zrozumieniu profilu obciążenia aplikacji funkcji poniżej przedstawiono konfiguracje, których można użyć w celu zwiększenia wydajności przepływności funkcji.

* [Asynchroniczne](#async)
* [Proces roboczy z wieloma językami](#use-multiple-language-worker-processes)
* [Maksymalna liczba procesów roboczych w ramach procesu roboczego języka](#set-up-max-workers-within-a-language-worker-process)
* [Pętla zdarzeń](#managing-event-loop)
* [Skalowanie w pionie](#vertical-scaling)



#### <a name="async"></a>Async

Ponieważ język [Python jest środowiskiem uruchomieniowym jednowątkowym](https://wiki.python.org/moin/GlobalInterpreterLock), wystąpienie hosta dla języka Python może domyślnie przetwarzać tylko jedno wywołanie funkcji. W przypadku aplikacji, które przetwarzają dużą liczbę zdarzeń we/wy i/lub są powiązane we/wy, można znacząco poprawić wydajność dzięki działaniu funkcji asynchronicznie.

Aby uruchomić funkcję asynchronicznie, należy użyć `async def` instrukcji, która uruchamia funkcję z [asyncio](https://docs.python.org/3/library/asyncio.html) bezpośrednio:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Oto przykład funkcji z wyzwalaczem HTTP, który używa klienta http [aiohttp](https://pypi.org/project/aiohttp/) :

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Funkcja bez `async` słowa kluczowego jest uruchamiana automatycznie w puli wątków ThreadPoolExecutor:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Aby w pełni korzystać z funkcji asynchronicznie, operacje we/wy i biblioteka, która jest używana w kodzie, muszą mieć również zaimplementowany przebieg asynchroniczny. Użycie synchronicznych operacji we/wy w funkcjach, które są zdefiniowane jako asynchroniczne, **może obniżyć** ogólną wydajność. Jeśli używane biblioteki nie mają wdrożonej wersji asynchronicznej, można nadal korzystać z asynchronicznego uruchamiania kodu przez [Zarządzanie pętlą zdarzeń](#managing-event-loop) w aplikacji. 

Poniżej przedstawiono kilka przykładów bibliotek klienckich, które mają zaimplementowany wzorzec asynchroniczny:
- [aiohttp](https://pypi.org/project/aiohttp/) — klient/serwer HTTP dla asyncio 
- [Strumienie API](https://docs.python.org/3/library/asyncio-stream.html) — na wysokim poziomie asynchroniczne/oczekujące — gotowe do pracy z połączeniem sieciowym
- Kolejka [Janus](https://pypi.org/project/janus/) — bezpieczna Kolejka asyncio-oparta na wątkach dla języka Python
- powiązania [pyzmq](https://pypi.org/project/pyzmq/) -Python dla ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Objaśnienie asynchroniczne w środowisku roboczym języka Python

Gdy zdefiniujesz `async` przed sygnaturą funkcji, Język Python oznaczy funkcję jako wspólną. Podczas wywoływania procedury wspólnej można ją zaplanować jako zadanie w pętli zdarzeń. Gdy wywołujesz `await` funkcję Async, rejestruje kontynuację w pętli zdarzeń i zezwala na pętlę zdarzeń, aby przetwarzać następne zadanie w czasie oczekiwania.

W naszym procesie roboczym języka Python proces roboczy udostępnia pętlę zdarzeń z `async` funkcją klienta i może obsługiwać wiele żądań współbieżnie. Zdecydowanie zachęcamy naszych klientów do korzystania z bibliotek zgodnych z asyncio (np. [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). Zastosowanie tych zaleceń znacznie zwiększy przepływność funkcji w porównaniu z tymi bibliotekami zaimplementowanymi synchronicznie.

> [!NOTE]
>  Jeśli funkcja jest zadeklarowana jako `async` bez żadnej `await` w jej implementacji, wydajność funkcji będzie poważnie miała wpływ, ponieważ pętla zdarzeń zostanie zablokowana, co uniemożliwi procesowi roboczemu języka Python obsługę współbieżnych żądań.

#### <a name="use-multiple-language-worker-processes"></a>Korzystanie z wielu procesów roboczych języka

Domyślnie każde wystąpienie hosta funkcji ma proces roboczy o pojedynczym języku. Liczbę procesów roboczych można zwiększyć na hosta (do 10) przy użyciu ustawienia aplikacji [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions następnie próbuje równomiernie rozpowszechnić jednoczesne wywołania funkcji przez tych pracowników.

W przypadku aplikacji powiązanych z procesorem CPU należy ustawić liczbę rdzeni, które są dostępne w ramach aplikacji funkcji. Aby dowiedzieć się więcej, zobacz [dostępne jednostki SKU wystąpienia](functions-premium-plan.md#available-instance-skus). 

Aplikacje powiązane we/wy mogą również zwiększyć liczbę procesów roboczych wykraczających poza liczbę dostępnych rdzeni. Należy pamiętać, że ustawienie liczby zbyt dużych procesów roboczych może mieć wpływ na ogólną wydajność ze względu na zwiększoną liczbę wymaganych przełączeń kontekstu. 

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który tworzy funkcje podczas skalowania aplikacji w celu spełnienia wymagań.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Skonfiguruj maksymalną liczbę procesów roboczych w ramach procesu roboczego języka

Jak wspomniano w [sekcji](#understanding-async-in-python-worker)asynchronicznej, proces roboczy języka Python traktuje [funkcje i](https://docs.python.org/3/library/asyncio-task.html#coroutines) współdziałanie inaczej. Procedura wspólna jest uruchamiana w ramach tej samej pętli zdarzeń, w której działa proces roboczy języka. Z drugiej strony wywołanie funkcji jest uruchamiane w [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), który jest obsługiwany przez proces roboczy języka, jako wątek.

Można ustawić maksymalną dozwoloną liczbę procesów roboczych na potrzeby uruchamiania funkcji synchronizacji przy użyciu ustawienia aplikacji [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) . Ta wartość ustawia `max_worker` argument obiektu ThreadPoolExecutor, który umożliwia językowi Python użycie puli najwyżej `max_worker` wątków do wykonywania wywołań asynchronicznie. `PYTHON_THREADPOOL_THREAD_COUNT`Dotyczy każdego procesu roboczego tworzonego przez hosta, a środowisko Python decyduje o tym, kiedy należy utworzyć nowy wątek lub ponownie użyć istniejącego wątku bezczynnego. W przypadku starszych wersji języka Python (czyli,, `3.8` `3.7` i `3.6` ) `max_worker` wartość jest ustawiona na 1. W przypadku wersji języka Python `3.9` `max_worker` jest ustawiony na `None` .

W przypadku aplikacji powiązanych z PROCESORem należy zachować wartość ustawienia niskiej, rozpoczynając od 1 i zwiększając się podczas eksperymentowania z obciążeniem. Ta sugestia polega na skróceniu czasu spędzonego na przełącznikach kontekstu i umożliwieniu zakończenia zadań związanych z PROCESORem.

W przypadku aplikacji powiązanych we/wy powinny być widoczne znaczne zyski, zwiększając liczbę wątków pracujących nad każdym wywołaniem. zalecamy rozpoczęcie od domyślnego ustawienia języka Python — liczbę rdzeni + 4, a następnie dostrajanie na podstawie wartości przepływności, które widzisz.

W przypadku aplikacji do obciążeń mieszanych należy zrównoważyć zarówno `FUNCTIONS_WORKER_PROCESS_COUNT` konfigurację, jak i `PYTHON_THREADPOOL_THREAD_COUNT` konfiguracje, aby zmaksymalizować przepływność. Aby zrozumieć, jak aplikacje funkcji spędzają najwięcej czasu, zalecamy ich profilowanie i ustawienie wartości zgodnie z zachowaniem. Zapoznaj się również z tą [sekcją](#use-multiple-language-worker-processes) , aby dowiedzieć się więcej o FUNCTIONS_WORKER_PROCESS_COUNT ustawieniach aplikacji.

> [!NOTE]
>  Chociaż te zalecenia dotyczą zarówno funkcji wyzwalanych przez protokół HTTP, jak i inne niż HTTP, może być konieczne dostosowanie innych określonych konfiguracji wyzwalających dla funkcji wyzwalanych przez protokół HTTP w celu uzyskania oczekiwanej wydajności z aplikacji funkcji. Aby uzyskać więcej informacji na ten temat, zapoznaj się z tym [artykułem](functions-best-practices.md).


#### <a name="managing-event-loop"></a>Zarządzanie pętlą zdarzeń

Należy używać asyncio zgodnych bibliotek innych firm. Jeśli żadna z bibliotek innych firm nie spełnia Twoich potrzeb, można także zarządzać pętlami zdarzeń w Azure Functions. Zarządzanie pętlami zdarzeń zapewnia większą elastyczność zarządzania zasobami obliczeniowymi, a także umożliwia Zawijanie synchronicznych bibliotek we/wy w ramach procedur.

Istnieje wiele przydatnych oficjalnych dokumentów w języku Python omawiających [współprocedury i zadania](https://docs.python.org/3/library/asyncio-task.html) i [pętlę zdarzeń](https://docs.python.org/3.8/library/asyncio-eventloop.html) przy użyciu wbudowanej biblioteki **asyncio** .

Skorzystaj z następującej biblioteki [żądań](https://github.com/psf/requests) jako przykładu, w tym fragmencie kodu jest używana biblioteka **asyncio** do zawijania `requests.get()` metody do współdziałania, co SAMPLE_URL umożliwia równoczesne uruchamianie wielu żądań sieci Web.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Skalowanie w pionie
W przypadku większej liczby jednostek przetwarzania, szczególnie w przypadku operacji związanych z procesorem CPU, można to zrobić przez uaktualnienie do planu Premium z wyższymi specyfikacjami. W przypadku wyższych jednostek przetwarzania można dostosować liczbę procesów roboczych zgodnie z liczbą dostępnych rdzeni i osiągnąć wyższy stopień równoległości. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programowania w języku Python Azure Functions, zobacz następujące zasoby:

* [Przewodnik dewelopera w języku Python Azure Functions](functions-reference-python.md)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)

