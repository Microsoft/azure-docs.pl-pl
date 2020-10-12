---
title: Wskazówki dotyczące opracowywania Azure Functions
description: Poznaj Azure Functions koncepcje i techniki, które są potrzebne do opracowania funkcji na platformie Azure, we wszystkich językach programowania i powiązaniach.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 9a3c0643f4fc965ff64106758320aeb445aaf9ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85921736"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions — przewodnik dla deweloperów
W Azure Functions określone funkcje udostępniają kilka podstawowych pojęć i składników technicznych, niezależnie od używanego języka lub powiązania. Przed przejściem do szczegółów szczegółowych informacji dotyczących danego języka lub powiązania należy zapoznać się z tym omówieniem, który ma zastosowanie do wszystkich z nich.

W tym artykule przyjęto założenie, że [przegląd Azure Functions](functions-overview.md)został już odczytany.

## <a name="function-code"></a>Kod funkcji
*Funkcja* jest podstawową koncepcją w Azure Functions. Funkcja zawiera dwa ważne elementy — kod, który można napisać w różnych językach, i kilka konfiguracji, function.jsna pliku. W przypadku skompilowanych języków ten plik konfiguracji jest generowany automatycznie z adnotacji w kodzie. W przypadku języków skryptów należy samodzielnie dostarczyć plik konfiguracji.

function.jsw pliku definiuje wyzwalacz funkcji, powiązania i inne ustawienia konfiguracji. Każda funkcja ma jeden i tylko jeden wyzwalacz. Środowisko uruchomieniowe używa tego pliku konfiguracji do określenia zdarzeń do monitorowania oraz przekazywania danych do i zwracania danych z wykonywania funkcji. Poniżej znajduje się przykład function.jspliku.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Aby uzyskać więcej informacji, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

`bindings`Właściwość służy do konfigurowania wyzwalaczy i powiązań. Każde powiązanie udostępnia kilka typowych ustawień i niektóre ustawienia, które są specyficzne dla określonego typu powiązania. Każde powiązanie wymaga następujących ustawień:

| Właściwość | Wartości/typy | Komentarze |
| --- | --- | --- |
| `type` |ciąg |Typ powiązania. Na przykład `queueTrigger`. |
| `direction` |"in", "out" |Wskazuje, czy powiązanie służy do otrzymywania danych do funkcji, czy wysyłania danych z funkcji. |
| `name` |ciąg |Nazwa, która jest używana dla powiązanych danych w funkcji. W języku C# jest to nazwa argumentu; w przypadku języka JavaScript jest to klucz na liście kluczy/wartości. |

## <a name="function-app"></a>Aplikacja funkcji
Aplikacja funkcji udostępnia kontekst wykonywania na platformie Azure, w którym działają funkcje. W związku z tym jest to jednostka wdrażania i zarządzania dla swoich funkcji. Aplikacja funkcji składa się z co najmniej jednej konkretnej funkcji, która jest zarządzana, wdrażana i skalowana ze sobą. Wszystkie funkcje w aplikacji funkcji mają ten sam plan cenowy, metodę wdrażania i wersję środowiska uruchomieniowego. Zastanów się nad aplikacją funkcji, aby zorganizować i wspólnie zarządzać funkcjami. Aby dowiedzieć się więcej, zobacz [jak zarządzać aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Wszystkie funkcje w aplikacji funkcji muszą zostać utworzone w tym samym języku. W [poprzednich wersjach](functions-versions.md) środowiska uruchomieniowego Azure Functions nie było to wymagane.

## <a name="folder-structure"></a>Struktura folderów
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Powyższa wartość to domyślna (i zalecana) struktura folderów dla aplikacji funkcji. Jeśli chcesz zmienić lokalizację pliku kodu funkcji, zmodyfikuj `scriptFile` sekcję _function.jsw_ pliku. Zalecamy również użycie [wdrożenia pakietu](deployment-zip-push.md) do wdrożenia projektu w aplikacji funkcji na platformie Azure. Możesz również korzystać z istniejących narzędzi, takich jak [ciągła integracja i wdrażanie](functions-continuous-deployment.md) oraz Azure DevOps.

> [!NOTE]
> Jeśli wdrażasz pakiet ręcznie, pamiętaj o wdrożeniu _host.jsw_ folderach plików i funkcji bezpośrednio do `wwwroot` folderu. Nie dołączaj `wwwroot` folderu we wdrożeniach. W przeciwnym razie możesz się dokończyć z `wwwroot\wwwroot` folderami.

#### <a name="use-local-tools-and-publishing"></a>Korzystanie z lokalnych narzędzi i publikowanie
Aplikacje funkcji można tworzyć i publikować przy użyciu różnych narzędzi, w tym [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [zaćmienie](./functions-create-maven-eclipse.md)i [Azure Functions Core Tools](./functions-develop-local.md). Aby uzyskać więcej informacji, zobacz temat [kod i test Azure Functions lokalnie](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Jak edytować funkcje w Azure Portal
Edytor funkcji wbudowanych w Azure Portal pozwala aktualizować kod i *function.jsna* pliku bezpośrednio w wierszu. Jest to zalecane tylko w przypadku małych zmian lub prób koncepcji — najlepszym rozwiązaniem jest użycie lokalnego narzędzia programistycznego, takiego jak VS Code.

## <a name="parallel-execution"></a>Wykonywanie równoległe
Gdy wielokrotne zdarzenia wyzwalające są wykonywane szybciej niż środowisko uruchomieniowe funkcji pojedynczego wątku, może je przetworzyć, ale środowisko uruchomieniowe może wielokrotnie wywołać funkcję.  Jeśli aplikacja funkcji korzysta z [planu hostingu zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work), aplikacja funkcji może automatycznie skalować w poziomie.  Każde wystąpienie aplikacji funkcji, niezależnie od tego, czy aplikacja działa zgodnie z planem hostingu zużycia czy regularnym [App Service planem hostingu](../app-service/overview-hosting-plans.md), może przetwarzać współbieżne wywołania funkcji równolegle przy użyciu wielu wątków.  Maksymalna liczba współbieżnych wywołań funkcji w każdym wystąpieniu aplikacji funkcji różni się w zależności od typu używanego wyzwalacza oraz zasobów używanych przez inne funkcje w aplikacji funkcji.

## <a name="functions-runtime-versioning"></a>Przechowywanie wersji środowiska uruchomieniowego funkcji

Wersję środowiska uruchomieniowego funkcji można skonfigurować przy użyciu `FUNCTIONS_EXTENSION_VERSION` Ustawienia aplikacji. Na przykład wartość "~ 3" wskazuje, że aplikacja funkcji będzie używać 3. x jako wersji głównej. Aplikacje funkcji są uaktualniane do każdej nowej wersji pomocniczej po ich wydaniu. Aby uzyskać więcej informacji, w tym o sposobie wyświetlania dokładnej wersji aplikacji funkcji, zobacz [jak dowiedzieć się, jak kierować Azure Functions wersje środowiska uruchomieniowego](set-runtime-version.md).

## <a name="repositories"></a>Repozytoria
Kod dla Azure Functions jest otwartym źródłem i przechowywany w repozytoriach usługi GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Host Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portal Azure Functions](https://github.com/azure/azure-functions-ux)
* [Szablony Azure Functions](https://github.com/azure/azure-functions-templates)
* [Zestaw SDK usługi Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozszerzenia zestawu SDK usługi Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Powiązania
Oto tabela wszystkich obsługiwanych powiązań.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Masz problemy z błędami pochodzącymi z powiązań? Zapoznaj się z dokumentacją dotyczącą [kodów błędów powiązań Azure Functions](functions-bindings-error-pages.md) .

## <a name="reporting-issues"></a>Raportowanie problemów
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Następne kroki
Więcej informacji można znaleźć w następujących zasobach:

* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](./functions-develop-local.md)
* [Najlepsze rozwiązania dotyczące Azure Functions](functions-best-practices.md)
* [Dokumentacja dla deweloperów Azure Functions C#](functions-dotnet-class-library.md)
* [Azure Functions Node.js Dokumentacja dla deweloperów](functions-reference-node.md)
