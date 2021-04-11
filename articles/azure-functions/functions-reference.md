---
title: Wskazówki dotyczące opracowywania Azure Functions
description: Poznaj Azure Functions koncepcje i techniki, które są potrzebne do opracowania funkcji na platformie Azure, we wszystkich językach programowania i powiązaniach.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614823"
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

| Właściwość    | Wartości | Typ | Komentarze|
|---|---|---|---|
| typ  | Nazwa powiązania.<br><br>Na przykład `queueTrigger`. | ciąg | |
| kierunek | `in`, `out`  | ciąg | Wskazuje, czy powiązanie służy do otrzymywania danych do funkcji, czy wysyłania danych z funkcji. |
| name | Identyfikator funkcji.<br><br>Na przykład `myQueue`. | ciąg | Nazwa, która jest używana dla powiązanych danych w funkcji. W języku C# jest to nazwa argumentu; w przypadku języka JavaScript jest to klucz na liście kluczy/wartości. |

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
Aplikacje funkcji można tworzyć i publikować przy użyciu różnych narzędzi, w tym [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [IntelliJ](./functions-create-maven-intellij.md), [zaćmienie](./functions-create-maven-eclipse.md)i [Azure Functions Core Tools](./functions-develop-local.md). Aby uzyskać więcej informacji, zobacz temat [kod i test Azure Functions lokalnie](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Jak edytować funkcje w Azure Portal
Edytor funkcji wbudowanych w Azure Portal pozwala aktualizować kod i *function.jsna* pliku bezpośrednio w wierszu. Jest to zalecane tylko w przypadku małych zmian lub prób koncepcji — najlepszym rozwiązaniem jest użycie lokalnego narzędzia programistycznego, takiego jak VS Code.

## <a name="parallel-execution"></a>Wykonywanie równoległe
Gdy wielokrotne zdarzenia wyzwalające są wykonywane szybciej niż środowisko uruchomieniowe funkcji pojedynczego wątku, może je przetworzyć, ale środowisko uruchomieniowe może wielokrotnie wywołać funkcję.  Jeśli aplikacja funkcji korzysta z [planu hostingu zużycia](event-driven-scaling.md), aplikacja funkcji może automatycznie skalować w poziomie.  Każde wystąpienie aplikacji funkcji, niezależnie od tego, czy aplikacja działa zgodnie z planem hostingu zużycia czy regularnym [App Service planem hostingu](../app-service/overview-hosting-plans.md), może przetwarzać współbieżne wywołania funkcji równolegle przy użyciu wielu wątków.  Maksymalna liczba współbieżnych wywołań funkcji w każdym wystąpieniu aplikacji funkcji różni się w zależności od typu używanego wyzwalacza oraz zasobów używanych przez inne funkcje w aplikacji funkcji.

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


## <a name="connections"></a>Połączenia

Projekt funkcji odwołuje się do informacji o połączeniu według nazwy od jej dostawcy konfiguracji. Nie akceptuje on bezpośrednio informacji o połączeniu, umożliwiając ich zmianę w różnych środowiskach. Na przykład definicja wyzwalacza może zawierać `connection` Właściwość. Może odnosić się do parametrów połączenia, ale nie można ustawić parametrów połączenia bezpośrednio w `function.json` . Zamiast tego należy ustawić `connection` nazwę zmiennej środowiskowej, która zawiera parametry połączenia.

Domyślny dostawca konfiguracji używa zmiennych środowiskowych. Mogą one być ustawiane przez [Ustawienia aplikacji](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) podczas uruchamiania usługi Azure Functions lub z [lokalnego pliku ustawień](functions-run-local.md#local-settings-file) podczas tworzenia lokalnego.

### <a name="connection-values"></a>Wartości połączenia

Gdy nazwa połączenia jest rozpoznawana jako jedna dokładna wartość, środowisko uruchomieniowe identyfikuje wartość jako _Parametry połączenia_, która zwykle zawiera wpis tajny. Szczegóły parametrów połączenia są definiowane przez usługę, z którą chcesz nawiązać połączenie.

Jednak nazwa połączenia może odwoływać się również do kolekcji wielu elementów konfiguracji. Zmienne środowiskowe mogą być traktowane jako kolekcja przy użyciu udostępnionego prefiksu kończącego się znakami podwójnego podkreślenia `__` . Do grupy można odwoływać się, ustawiając nazwę połączenia na ten prefiks.

Na przykład `connection` Właściwość dla definicji wyzwalacza obiektu blob platformy Azure może być `Storage1` . Pod warunkiem `Storage1` , że `Storage1__serviceUri` dla właściwości połączenia zostanie użyta wartość pojedynczego ciągu, która została skonfigurowana jako nazwa `serviceUri` . Właściwości połączenia są różne dla każdej usługi. Zapoznaj się z dokumentacją rozszerzenia, które korzysta z tego połączenia.

### <a name="configure-an-identity-based-connection"></a>Konfigurowanie połączenia opartego na tożsamościach

Niektóre połączenia w Azure Functions są skonfigurowane do używania tożsamości zamiast wpisu tajnego. Obsługa jest zależna od rozszerzenia przy użyciu połączenia. W niektórych przypadkach parametry połączenia nadal mogą być wymagane w funkcjach, mimo że usługa, z którą nawiązujesz połączenie, obsługuje połączenia oparte na tożsamości.

> [!IMPORTANT]
> Nawet jeśli rozszerzenie powiązania obsługuje połączenia oparte na tożsamościach, ta konfiguracja może nie być jeszcze obsługiwana w planie zużycia. Zapoznaj się z poniższą tabelą pomocy technicznej.

Połączenia oparte na tożsamości są obsługiwane przez następujący wyzwalacz i rozszerzenia powiązań:

| Nazwa rozszerzenia | Wersja rozszerzenia                                                                                     | Obsługiwane w planie zużycia |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Obiekt bob Azure     | [Wersja 5.0.0-beta1 lub nowsza](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | Nie                                    |
| Azure Queue    | [Wersja 5.0.0-beta1 lub nowsza](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | Nie                                    |
| Azure Event Hubs    | [Wersja 5.0.0-beta1 lub nowsza](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | Nie                                    |

> [!NOTE]
> Obsługa połączeń opartych na tożsamościach nie jest jeszcze dostępna dla połączeń magazynu używanych przez środowisko uruchomieniowe funkcji dla zachowań podstawowych. Oznacza to, że `AzureWebJobsStorage` ustawienie musi być parametrami połączenia.

#### <a name="connection-properties"></a>Connection properties (Właściwości połączenia)

Połączenie oparte na tożsamości dla usługi platformy Azure akceptuje następujące właściwości:

| Właściwość    | Wymagane dla rozszerzeń | Zmienna środowiskowa | Opis |
|---|---|---|---|
| Identyfikator URI usługi | Obiekt blob platformy Azure, kolejka platformy Azure | `<CONNECTION_NAME_PREFIX>__serviceUri` |  Identyfikator URI płaszczyzny danych usługi, z którą nawiązujesz połączenie. |
| W pełni kwalifikowana przestrzeń nazw | Event Hubs | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | W pełni kwalifikowana przestrzeń nazw centrum zdarzeń. |

Dodatkowe opcje mogą być obsługiwane dla danego typu połączenia. Zapoznaj się z dokumentacją składnika, który nawiązuje połączenie.

W przypadku hostowania w usłudze Azure Functions połączenia oparte na tożsamościach korzystają z [tożsamości zarządzanej](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). Domyślnie używana jest tożsamość przypisana do systemu. W przypadku uruchamiania w innych kontekstach, takich jak lokalne programowanie, tożsamość dewelopera jest używana zamiast tego, chociaż można ją dostosować przy użyciu alternatywnych parametrów połączenia.

##### <a name="local-development"></a>Programowanie lokalne

W przypadku uruchomienia lokalnego powyższej konfiguracji nakazuje środowisko uruchomieniowe na korzystanie z lokalnej tożsamości dewelopera. Połączenie spróbuje uzyskać token z następujących lokalizacji:

- Lokalna pamięć podręczna współdzielona przez aplikacje firmy Microsoft
- Bieżący kontekst użytkownika w programie Visual Studio
- Bieżący kontekst użytkownika w Visual Studio Code
- Bieżący kontekst użytkownika w interfejsie wiersza polecenia platformy Azure

Jeśli żadna z tych opcji nie powiedzie się, wystąpi błąd.

W niektórych przypadkach warto określić użycie innej tożsamości. Możesz dodać właściwości konfiguracji dla połączenia, które wskazuje na alternatywną tożsamość.

> [!NOTE]
> Następujące opcje konfiguracji nie są obsługiwane, jeśli są hostowane w usłudze Azure Functions.

Aby nawiązać połączenie przy użyciu jednostki usługi Azure Active Directory z IDENTYFIKATORem klienta i kluczem tajnym, zdefiniuj połączenie z następującymi wymaganymi właściwościami oprócz [Właściwości połączenia](#connection-properties) powyżej:

| Właściwość    | Zmienna środowiskowa | Opis |
|---|---|---|
| Identyfikator dzierżawy | `<CONNECTION_NAME_PREFIX>__tenantId` | Identyfikator dzierżawy Azure Active Directory (katalog). |
| Identyfikator klienta | `<CONNECTION_NAME_PREFIX>__clientId` |  Identyfikator klienta (aplikacji) rejestracji aplikacji w dzierżawie. |
| Klucz tajny klienta | `<CONNECTION_NAME_PREFIX>__clientSecret` | Wpis tajny klienta wygenerowany dla rejestracji aplikacji. |

Przykład `local.settings.json` właściwości wymaganych dla połączenia opartego na tożsamościach z obiektem blob platformy Azure: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Udziel uprawnienia do tożsamości

Tożsamość, która jest używana, musi mieć uprawnienia do wykonywania zamierzonych akcji. Jest to zazwyczaj realizowane przez przypisanie roli w ramach RBAC platformy Azure lub określenie tożsamości w zasadach dostępu, w zależności od usługi, z którą nawiązujesz połączenie. Zapoznaj się z dokumentacją dla każdej usługi, na której są potrzebne uprawnienia i jak można je ustawić.

> [!IMPORTANT]
> Niektóre uprawnienia mogą być udostępniane przez usługę, która nie jest wymagana dla wszystkich kontekstów. Jeśli to możliwe, przestrzegaj **zasad najniższych uprawnień**, przyznając tożsamości tylko wymagane uprawnienia. Jeśli na przykład aplikacja musi odczytywać dane z obiektu BLOB, należy użyć roli [czytnika danych obiektów blob magazynu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) jako [właściciela danych obiektu blob magazynu](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) , który zawiera nadmierne uprawnienia do operacji odczytu.


## <a name="reporting-issues"></a>Raportowanie problemów
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Następne kroki
Więcej informacji można znaleźć w następujących zasobach:

* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](./functions-develop-local.md)
* [Najlepsze rozwiązania dotyczące Azure Functions](functions-best-practices.md)
* [Dokumentacja dla deweloperów Azure Functions C#](functions-dotnet-class-library.md)
* [Azure Functions Node.js Dokumentacja dla deweloperów](functions-reference-node.md)
