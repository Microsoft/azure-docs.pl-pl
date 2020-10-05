---
title: Rozliczenia funkcji trwałych — Azure Functions
description: Zapoznaj się z wewnętrznymi zachowaniami Durable Functions i wpływem rozliczeń za Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "74233009"
---
# <a name="durable-functions-billing"></a>Rozliczanie Durable Functions

[Durable Functions](durable-functions-overview.md) jest rozliczany w taki sam sposób jak Azure Functions. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Podczas wykonywania funkcji programu Orchestrator w [planie zużycia](../functions-scale.md#consumption-plan)Azure Functions należy mieć świadomość niektórych zachowań rozliczania. W poniższych sekcjach opisano te zachowania i ich skutki bardziej szczegółowo.

## <a name="orchestrator-function-replay-billing"></a>Rozliczanie powtarzania funkcji programu Orchestrator

[Funkcje programu Orchestrator](durable-functions-orchestrations.md) mogą być odtwarzane kilka razy w okresie istnienia aranżacji. Każde powtórzenie jest wyświetlane przez środowisko uruchomieniowe Azure Functions jako odrębne wywołanie funkcji. Z tego powodu w planie zużycia Azure Functions naliczane są opłaty za każde powtórzenie funkcji programu Orchestrator. W przypadku innych typów planów nie są naliczane opłaty za odtwarzanie funkcji programu Orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Oczekiwanie na funkcje programu Orchestrator i ich uzyskanie

Gdy funkcja programu Orchestrator czeka na zakończenie akcji asynchronicznej przy użyciu **oczekiwania** w języku C# lub **Yield** w języku JavaScript, środowisko uruchomieniowe uważa, że określone wykonanie zostało zakończone. Rozliczenia dla funkcji programu Orchestrator są zatrzymane w tym momencie. Nie zostanie on wznowiony do momentu ponownego uruchomienia następnej funkcji programu Orchestrator. Nie są naliczane opłaty za każdy czas spędzony na czekaniu lub w funkcji programu Orchestrator.

> [!NOTE]
> Funkcje wywołujące inne funkcje są uznawane za antywzorców przez niektóre. Wynika to z faktu, że występuje problem znany jako _podwójne rozliczenia_. Gdy funkcja wywołuje inną funkcję bezpośrednio, oba są uruchamiane w tym samym czasie. Wywoływana funkcja aktywnie uruchamia kod, podczas gdy wywoływana funkcja oczekuje na odpowiedź. W takim przypadku należy zapłacić za czas, przez który funkcja wywołująca czeka na uruchomienie wywołanej funkcji.
>
> W funkcjach programu Orchestrator nie ma podwójnego rozliczania. Rozliczenia funkcji programu Orchestrator są przerywane podczas oczekiwania na wynik działania funkcji lub aranżacji podrzędnej.

## <a name="durable-http-polling"></a>Trwałe sondowanie HTTP

Funkcje programu Orchestrator mogą wykonywać długotrwałe wywołania HTTP do zewnętrznych punktów końcowych, zgodnie z opisem w [artykule funkcje http](durable-functions-http-features.md). Metoda **CallHttpAsync** w języku C# i Metoda **CallHttp** w języku JavaScript mogą wewnętrznie sondować punkt końcowy HTTP przy użyciu [wzorca asynchronicznego 202](durable-functions-http-features.md#http-202-handling).

Obecnie nie ma bezpośredniego rozliczania dla wewnętrznych operacji sondowania protokołu HTTP. Jednak sondowanie wewnętrzne może spowodować okresowe powtarzanie funkcji programu Orchestrator. Zostaną naliczone opłaty za te standardowe funkcje wewnętrzne.

## <a name="azure-storage-transactions"></a>Transakcje usługi Azure Storage

Durable Functions domyślnie używa usługi Azure Storage do przechowywania stanu trwałego, przetwarzania komunikatów i zarządzania partycjami za pośrednictwem dzierżaw obiektów BLOB. Ponieważ masz konto magazynu, wszystkie koszty transakcji są rozliczane w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji na temat artefaktów usługi Azure Storage używanych przez Durable Functions, zobacz artykuł dotyczący [centrów zadań](durable-functions-task-hubs.md).

Niektóre czynniki przyczyniają się do rzeczywistych kosztów usługi Azure Storage ponoszonych przez aplikację Durable Functions:

* Pojedyncza aplikacja funkcji jest skojarzona z jednym centrum zadań, który udostępnia zestaw zasobów usługi Azure Storage. Te zasoby są używane przez wszystkie funkcje trwałe w aplikacji funkcji. Rzeczywista liczba funkcji w aplikacji funkcji nie ma wpływu na koszty transakcji usługi Azure Storage.
* Każde wystąpienie aplikacji funkcji wewnętrznie sonduje wiele kolejek na koncie magazynu przy użyciu algorytmu sondowania wykładniczego wycofywania. Wystąpienie aplikacji bezczynne sonduje kolejki rzadziej niż w przypadku aktywnej aplikacji, co powoduje zmniejszenie kosztów transakcji. Aby uzyskać więcej informacji na temat zachowania sondowania kolejki Durable Functions, zobacz [sekcję sondowanie kolejki w artykule dotyczącym wydajności i skalowania](durable-functions-perf-and-scale.md#queue-polling).
* Po uruchomieniu w planach użycia Azure Functions lub Premium [kontroler skalowania Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) regularnie sonduje wszystkie kolejki centrum zadań w tle. Jeśli aplikacja funkcji jest pod kątem średniej skali, tylko pojedyncze wystąpienie kontrolera skalowania będzie sondował te kolejki. Jeśli aplikacja funkcji jest skalowana do dużej liczby wystąpień, można dodać więcej wystąpień kontrolera skalowania. Te dodatkowe wystąpienia kontrolera skalowania mogą zwiększyć łączną liczbę kosztów transakcji kolejki.
* Każde wystąpienie aplikacji funkcji konkuruje z zestawem dzierżaw obiektów BLOB. Te wystąpienia będą okresowo podejmować wywołania do Blob service platformy Azure, aby odnowić dzierżawy lub próbować uzyskać nowe dzierżawy. Liczba partycji skonfigurowana przez centrum zadań określa liczbę dzierżaw obiektów BLOB. Skalowanie w górę do większej liczby wystąpień aplikacji może spowodować zwiększenie kosztów transakcji usługi Azure Storage skojarzonych z tymi operacjami dzierżawy.

Więcej informacji na temat cennika usługi Azure Storage można znaleźć w dokumentacji [cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) . 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure Functions cenach](https://azure.microsoft.com/pricing/details/functions/)
