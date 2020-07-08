---
title: Rejestrowanie rozszerzeń powiązań Azure Functions
description: Dowiedz się, jak zarejestrować rozszerzenie powiązania Azure Functions w oparciu o środowisko.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84697344"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązań Azure Functions

W Azure Functions w wersji 2. x [powiązania](./functions-triggers-bindings.md) są dostępne jako oddzielne pakiety w środowisku uruchomieniowym funkcji. Chociaż usługa .NET Functions uzyskuje dostęp do powiązań za pomocą pakietów NuGet, zbiory rozszerzeń umożliwiają innym funkcjom dostęp do wszystkich powiązań za pomocą ustawienia konfiguracji.

Należy wziąć pod uwagę następujące elementy związane z rozszerzeniami powiązań:

- Rozszerzenia powiązań nie są jawnie zarejestrowane w funkcjach 1. x, chyba że podczas [tworzenia biblioteki klas języka C# przy użyciu programu Visual Studio](#local-csharp).

- Wyzwalacze protokołu HTTP i czasomierze są domyślnie obsługiwane i nie wymagają rozszerzenia.

Poniższa tabela zawiera informacje o tym, kiedy i w jaki sposób rejestrujesz powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w funkcjach 1. x  |Rejestracja<br/> w funkcjach 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatyczny|Automatyczny|
|Programowanie w języku Non-.NET lub lokalne podstawowe narzędzia platformy Azure|Automatyczny|[Użyj Azure Functions Core Tools i pakietów rozszerzeń](#extension-bundles)|
|Biblioteka klas języka C# przy użyciu programu Visual Studio|[Korzystanie z narzędzi NuGet](#vs)|[Korzystanie z narzędzi NuGet](#vs)|
|Biblioteka klas języka C# używająca Visual Studio Code|Nie dotyczy|[Użyj interfejs wiersza polecenia platformy .NET Core](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Zestawy rozszerzeń do lokalnego tworzenia

Pakiety rozszerzeń to technologia wdrażania, która umożliwia dodawanie zgodnego zestawu funkcji powiązań dla aplikacji funkcji. Podczas kompilowania aplikacji dodawane są wstępnie zdefiniowane rozszerzenia. Pakiety rozszerzeń zdefiniowane w pakiecie są zgodne ze sobą, co pomaga uniknąć konfliktów między pakietami. Pakiety rozszerzeń można włączyć w host.jsaplikacji w pliku.  

Można używać pakietów rozszerzeń z wersją 2. x i nowszymi wersjami środowiska uruchomieniowego Functions. Podczas programowania lokalnego upewnij się, że używasz najnowszej wersji [Azure Functions Core Tools](functions-run-local.md#v2).

Używaj pakietów rozszerzeń do lokalnego tworzenia przy użyciu Azure Functions Core Tools, Visual Studio Code i podczas zdalnego kompilowania.

Jeśli nie używasz zestawów rozszerzeń, przed zainstalowaniem rozszerzeń powiązań należy zainstalować zestaw SDK programu .NET Core 2. x na komputerze lokalnym. Pakiety rozszerzeń usuwają to wymaganie na potrzeby lokalnego projektowania. 

Aby użyć pakietów rozszerzeń, zaktualizuj *host.jsw* pliku, aby uwzględnić następujący wpis dla `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Biblioteka klas C z programem Visual Studio

W programie **Visual Studio**można zainstalować pakiety z konsoli Menedżera pakietów za pomocą polecenia [install-package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , jak pokazano w następującym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Nazwa pakietu używana dla danego powiązania znajduje się w artykule referencyjnym dla tego powiązania. Aby zapoznać się z przykładem, zobacz [sekcję Packages artykułu dotyczącego powiązania Service Bus](functions-bindings-service-bus.md#functions-1x).

Zastąp `<TARGET_VERSION>` w przykładzie konkretną wersją pakietu, taką jak `3.0.0-beta5` . Prawidłowe wersje są wymienione na stronach poszczególnych pakietów w witrynie [NuGet.org](https://nuget.org). Wersje główne, które odnoszą się do środowiska uruchomieniowego Functions 1. x lub 2. x, są określone w artykule referencyjnym dla powiązania.

Jeśli używasz `Install-Package` , aby odwołać się do powiązania, nie musisz używać [pakietów rozszerzeń](#extension-bundles). To podejście jest specyficzne dla bibliotek klas skompilowanych w programie Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Biblioteka klas języka C# z Visual Studio Code

W **Visual Studio Code**Zainstaluj pakiety dla projektu biblioteki klas C# z wiersza polecenia przy użyciu polecenia [dotnet add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) w interfejs wiersza polecenia platformy .NET Core. Poniższy przykład ilustruje sposób dodawania powiązania:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Interfejs wiersza polecenia platformy .NET Core można używać tylko na potrzeby programowania Azure Functions 2. x.

Zamień `<BINDING_TYPE_NAME>` na nazwę pakietu zawierającego wymagane powiązanie. Żądany artykuł referencyjny powiązania można znaleźć na [liście obsługiwanych powiązań](./functions-triggers-bindings.md#supported-bindings).

Zastąp `<TARGET_VERSION>` w przykładzie konkretną wersją pakietu, taką jak `3.0.0-beta5` . Prawidłowe wersje są wymienione na stronach poszczególnych pakietów w witrynie [NuGet.org](https://nuget.org). Wersje główne, które odnoszą się do środowiska uruchomieniowego Functions 1. x lub 2. x, są określone w artykule referencyjnym dla powiązania.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Przykład wyzwalacza i powiązania funkcji platformy Azure](./functions-bindings-example.md)
