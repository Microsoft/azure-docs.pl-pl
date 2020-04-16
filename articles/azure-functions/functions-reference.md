---
title: Wskazówki dotyczące tworzenia funkcji platformy Azure
description: Poznaj koncepcje i techniki usługi Azure Functions, których potrzebujesz do tworzenia funkcji na platformie Azure we wszystkich językach programowania i powiązaniach.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: b6af3d7ab1fdd35391c9a189162c57dfb259f2d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405353"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions — przewodnik dla deweloperów
W usłudze Azure Functions określone funkcje współużytkują kilka podstawowych pojęć technicznych i składników, niezależnie od używanego języka lub powiązania. Zanim przejdziesz do szczegółów nauki specyficznych dla danego języka lub powiązania, zapoznaj się z tym omówieniem, który dotyczy wszystkich z nich.

W tym artykule założono, że zostały już przeczytane [usługi Azure Functions omówienie.](functions-overview.md)

## <a name="function-code"></a>Kod funkcji
*Funkcja* jest podstawową koncepcją w usłudze Azure Functions. Funkcja zawiera dwa ważne elementy - kod, który może być napisany w różnych językach, a niektóre config, plik function.json. W przypadku skompilowanych języków ten plik konfiguracyjny jest generowany automatycznie z adnotacji w kodzie. W przypadku języków skryptowych należy samodzielnie podać plik konfiguracyjny.

Plik function.json definiuje wyzwalacz funkcji, powiązania i inne ustawienia konfiguracji. Każda funkcja ma jeden i tylko jeden wyzwalacz. Środowisko wykonawcze używa tego pliku konfiguracyjnego do określenia zdarzeń do monitorowania i sposobu przekazywania danych do i zwracania danych z wykonania funkcji. Poniżej przedstawiono przykładowy plik function.json.

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

Aby uzyskać więcej informacji, zobacz [Usługi Azure Functions wyzwalania i pojęć powiązań](functions-triggers-bindings.md).

Właściwość `bindings` jest, gdzie można skonfigurować wyzwalacze i powiązania. Każde powiązanie udostępnia kilka typowych ustawień i niektóre ustawienia, które są specyficzne dla określonego typu powiązania. Każde powiązanie wymaga następujących ustawień:

| Właściwość | Wartości/typy | Komentarze |
| --- | --- | --- |
| `type` |ciąg |Typ wiązania. Na przykład `queueTrigger`. |
| `direction` |"in", "out" |Wskazuje, czy powiązanie służy do odbierania danych do funkcji lub wysyłania danych z funkcji. |
| `name` |ciąg |Nazwa używana dla danych powiązanych w funkcji. W przypadku języka C#jest to nazwa argumentu; dla JavaScript, jest to klucz na liście kluczy/wartości. |

## <a name="function-app"></a>Aplikacja funkcji
Aplikacja funkcji zapewnia kontekst wykonywania na platformie Azure, w którym działają funkcje. W związku z tym jest to jednostka wdrażania i zarządzania dla funkcji. Aplikacja funkcji składa się z jednej lub więcej poszczególnych funkcji, które są zarządzane, wdrażane i skalowane razem. Wszystkie funkcje w aplikacji funkcji współużytkują ten sam plan cenowy, metodę wdrażania i wersję środowiska uruchomieniowego. Aplikacja funkcji może być sposobem organizowania i zbiorowego zarządzania funkcjami. Aby dowiedzieć się więcej, zobacz [Jak zarządzać aplikacją funkcyjną](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Wszystkie funkcje w aplikacji funkcji muszą być autorstwa w tym samym języku. W [poprzednich wersjach](functions-versions.md) środowiska uruchomieniowego usługi Azure Functions nie było to wymagane.

## <a name="folder-structure"></a>Struktura folderów
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Powyżej jest domyślna (i zalecane) struktura folderów dla aplikacji funkcji. Jeśli chcesz zmienić lokalizację pliku kodu funkcji, zmodyfikuj sekcję `scriptFile` pliku _function.json._ Zalecamy również użycie [wdrażania pakietu,](deployment-zip-push.md) aby wdrożyć projekt w aplikacji funkcji na platformie Azure. Można również użyć istniejących narzędzi, takich jak [ciągła integracja i wdrażanie](functions-continuous-deployment.md) oraz Azure DevOps.

> [!NOTE]
> Jeśli pakiet jest wdrażany ręcznie, należy wdrożyć plik _host.json_ i `wwwroot` foldery funkcji bezpośrednio do folderu. Nie należy `wwwroot` dołączać folderu do wdrożeń. W przeciwnym razie `wwwroot\wwwroot` skończysz z folderami.

#### <a name="use-local-tools-and-publishing"></a>Korzystanie z narzędzi lokalnych i publikowania
Aplikacje funkcyjne mogą być tworzyć i publikowane przy użyciu różnych narzędzi, w tym [Visual Studio,](./functions-develop-vs.md) [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md)i Azure Functions [Core Tools](./functions-develop-local.md). Aby uzyskać więcej informacji, zobacz [Kod i testowanie usług Azure Functions lokalnie.](./functions-develop-local.md)

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a>Jak edytować funkcje w witrynie Azure portal
Edytor funkcji wbudowany w witrynę Azure portal umożliwia aktualizowanie kodu i pliku *function.json* bezpośrednio w linii. Jest to zalecane tylko w przypadku małych zmian lub dowodów koncepcji — najlepszym rozwiązaniem jest użycie narzędzia do tworzenia lokalnego, takiego jak VS Code.

## <a name="parallel-execution"></a>Równoległego
Gdy wiele zdarzeń wyzwalania występuje szybciej niż środowisko uruchomieniowe funkcji jednowątkowej może je przetwarzać, środowisko wykonawcze może wywołać funkcję wiele razy równolegle.  Jeśli aplikacja funkcji korzysta z [planu hostingu zużycie,](functions-scale.md#how-the-consumption-and-premium-plans-work)aplikacja funkcji może automatycznie skalować w poziomie.  Każde wystąpienie aplikacji funkcji, niezależnie od tego, czy aplikacja działa w planie hostingu zużycie lub zwykły [plan hostingu usługi App Service,](../app-service/overview-hosting-plans.md)może przetwarzać wywołania równoczesnych funkcji równolegle przy użyciu wielu wątków.  Maksymalna liczba wywołań funkcji równoczesnych w każdym wystąpieniu aplikacji funkcji różni się w zależności od typu wyzwalacza używanego, a także zasobów używanych przez inne funkcje w aplikacji funkcji.

## <a name="functions-runtime-versioning"></a>Funkcje przechowywania wersji środowiska wykonawczego

Wersję środowiska wykonawczego funkcji można skonfigurować `FUNCTIONS_EXTENSION_VERSION` przy użyciu ustawienia aplikacji. Na przykład wartość "~3" wskazuje, że aplikacja function użyje 3.x jako wersji głównej. Aplikacje funkcji są uaktualniane do każdej nowej wersji pomocniczej, ponieważ są one wydawane. Aby uzyskać więcej informacji, w tym jak wyświetlić dokładną wersję aplikacji funkcji, zobacz [Jak kierować wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Repozytoria
Kod usługi Azure Functions jest open source i przechowywany w repozytoriach GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Host usług Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portal usług Azure Functions](https://github.com/azure/azure-functions-ux)
* [Szablony funkcji platformy Azure](https://github.com/azure/azure-functions-templates)
* [Zestaw SDK usługi Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozszerzenia zestawu SDK usługi Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Powiązania
Oto tabela wszystkich obsługiwanych powiązań.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Masz problemy z błędami pochodzącymi z powiązań? Przejrzyj dokumentację [kodów błędów powiązania funkcji platformy Azure.](functions-bindings-error-pages.md)

## <a name="reporting-issues"></a>Zgłaszanie problemów
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Następne kroki
Więcej informacji zawierają następujące zasoby:

* [Wyzwalacze i powiązania usługi Azure Functions](functions-triggers-bindings.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](./functions-develop-local.md)
* [Najważniejsze wskazówki dotyczące funkcji platformy Azure](functions-best-practices.md)
* [Odwołanie do dewelopera usług Azure Functions C#](functions-dotnet-class-library.md)
* [Odwołanie do dewelopera usługi Azure Functions Node.js](functions-reference-node.md)
