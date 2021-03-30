---
title: Rejestrowanie rozszerzeń powiązań Azure Functions
description: Dowiedz się, jak zarejestrować rozszerzenie powiązania Azure Functions w oparciu o środowisko.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689569"
---
# <a name="register-azure-functions-binding-extensions"></a>Rejestrowanie rozszerzeń powiązań Azure Functions

Począwszy od Azure Functions wersja 2. x, środowisko uruchomieniowe Functions domyślnie obejmuje wyzwalacze HTTP i czasomierz. Inne [wyzwalacze i powiązania](./functions-triggers-bindings.md) są dostępne jako oddzielne pakiety.

Aplikacje funkcji biblioteki klas .NET używają powiązań, które są zainstalowane w projekcie jako pakiety NuGet. Zestawy rozszerzeń umożliwiają aplikacjom funkcji non-.NET korzystanie z tych samych powiązań bez konieczności zajmowania się infrastrukturą .NET.

Poniższa tabela zawiera informacje o tym, kiedy i w jaki sposób rejestrujesz powiązania.

| Środowisko deweloperskie |Rejestracja<br/> w funkcjach 1. x  |Rejestracja<br/> w funkcjach 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatyczny|Automatyczne<sup>*</sup>|
|Języki Non-.NET|Automatyczny|Użyj [pakietów rozszerzeń](#extension-bundles) (zalecane) lub [jawnie Zainstaluj rozszerzenia](#explicitly-install-extensions)|
|Biblioteka klas języka C# przy użyciu programu Visual Studio|[Korzystanie z narzędzi NuGet](#vs)|[Korzystanie z narzędzi NuGet](#vs)|
|Biblioteka klas języka C# używająca Visual Studio Code|Nie dotyczy|[Użyj interfejs wiersza polecenia platformy .NET Core](#vs-code)|

<sup>*</sup> Portal używa pakietów rozszerzeń.

## <a name="access-extensions-in-non-net-languages"></a>Rozszerzenia dostępu w językach non-.NET

W przypadku aplikacji Java, JavaScript, PowerShell, Python i niestandardowych funkcji obsługi zaleca się używanie pakietów rozszerzeń do uzyskiwania dostępu do powiązań. W przypadkach, gdy nie można używać pakietów rozszerzeń, można jawnie zainstalować rozszerzenia powiązań.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Pakiety rozszerzeń

Zestawy rozszerzeń umożliwiają dodawanie zgodnego zestawu rozszerzeń powiązań do aplikacji funkcji. Pakiety rozszerzeń można włączyć w *host.jsaplikacji w* pliku.

Można używać pakietów rozszerzeń z wersją 2. x i nowszymi wersjami środowiska uruchomieniowego Functions.

Pakiety rozszerzeń są w wersji. Każda wersja zawiera określony zestaw rozszerzeń powiązań, które są sprawdzane, aby współdziałać ze sobą. Wybierz wersję pakietu na podstawie rozszerzeń, które są potrzebne w aplikacji.

Aby dodać pakiet rozszerzeń do aplikacji funkcji, Dodaj `extensionBundle` sekcję do *host.js*. W wielu przypadkach Visual Studio Code i Azure Functions Core Tools będą automatycznie dodawać do Ciebie.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

W poniższej tabeli wymieniono aktualnie dostępne wersje domyślnego pakietu *Microsoft. Azure. Functions. ExtensionBundle* oraz linki do rozszerzeń, które zawierają.

| Wersja pakietu | Wersja w host.jsna | Dołączone rozszerzenia |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Aby wygenerować pakiet, zobacz [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |
| 2.x | `[2.*, 3.0.0)` | Aby wygenerować pakiet, zobacz [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) |

> [!NOTE]
> Chociaż możesz określić niestandardowy zakres wersji w host.jsna, zalecamy użycie wartości wersji z tej tabeli.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Jawne Instalowanie rozszerzeń

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Instalowanie rozszerzeń z programu NuGet w językach .NET

W przypadku projektu opartego na bibliotece klas języka C# rozszerzenia należy instalować bezpośrednio. Zestawy rozszerzeń są przeznaczone specjalnie dla projektów, które nie są oparte na bibliotece klas C#.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Biblioteka klas C z programem Visual Studio

W programie **Visual Studio** można zainstalować pakiety z konsoli Menedżera pakietów za pomocą polecenia [install-package](/nuget/tools/ps-ref-install-package) , jak pokazano w następującym przykładzie:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Nazwa pakietu używana dla danego powiązania znajduje się w artykule referencyjnym dla tego powiązania. Aby zapoznać się z przykładem, zobacz [sekcję Packages artykułu dotyczącego powiązania Service Bus](functions-bindings-service-bus.md#functions-1x).

Zastąp `<TARGET_VERSION>` w przykładzie konkretną wersją pakietu, taką jak `3.0.0-beta5` . Prawidłowe wersje są wymienione na stronach poszczególnych pakietów w witrynie [NuGet.org](https://nuget.org). Wersje główne, które odnoszą się do środowiska uruchomieniowego Functions 1. x lub 2. x, są określone w artykule referencyjnym dla powiązania.

Jeśli używasz `Install-Package` , aby odwołać się do powiązania, nie musisz używać [pakietów rozszerzeń](#extension-bundles). To podejście jest specyficzne dla bibliotek klas skompilowanych w programie Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Biblioteka klas języka C# z Visual Studio Code

W **Visual Studio Code** Zainstaluj pakiety dla projektu biblioteki klas C# z wiersza polecenia przy użyciu polecenia [dotnet add Package](/dotnet/core/tools/dotnet-add-package) w interfejs wiersza polecenia platformy .NET Core. Poniższy przykład ilustruje sposób dodawania powiązania:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Interfejs wiersza polecenia platformy .NET Core można używać tylko na potrzeby programowania Azure Functions 2. x.

Zamień `<BINDING_TYPE_NAME>` na nazwę pakietu zawierającego wymagane powiązanie. Żądany artykuł referencyjny powiązania można znaleźć na [liście obsługiwanych powiązań](./functions-triggers-bindings.md#supported-bindings).

Zastąp `<TARGET_VERSION>` w przykładzie konkretną wersją pakietu, taką jak `3.0.0-beta5` . Prawidłowe wersje są wymienione na stronach poszczególnych pakietów w witrynie [NuGet.org](https://nuget.org). Wersje główne, które odnoszą się do środowiska uruchomieniowego Functions 1. x lub 2. x, są określone w artykule referencyjnym dla powiązania.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Przykład wyzwalacza i powiązania funkcji platformy Azure](./functions-bindings-example.md)
