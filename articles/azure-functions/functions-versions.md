---
title: Przegląd wersji środowiska uruchomieniowego Azure Functions
description: Azure Functions obsługuje wiele wersji środowiska uruchomieniowego. Poznaj różnice między nimi i wybierz odpowiedni dla siebie.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: b37cf33a96452f9f3e86f853d3d87fd3b4b3879c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431855"
---
# <a name="azure-functions-runtime-versions-overview"></a>Przegląd wersji środowiska uruchomieniowego Azure Functions

Azure Functions obecnie obsługuje trzy wersje hosta środowiska uruchomieniowego: 3. x, 2. x i 1. x. Wszystkie trzy wersje są obsługiwane w scenariuszach produkcyjnych.  

> [!IMPORTANT]
> Wersja 1. x jest w trybie konserwacji i obsługuje programowanie tylko w Azure Portal, Azure Stack Hub lub lokalnie na komputerach z systemem Windows. Ulepszenia są dostępne tylko w nowszych wersjach. 

W tym artykule opisano niektóre różnice między różnymi wersjami, sposoby tworzenia poszczególnych wersji oraz zmiany wersji.

## <a name="languages"></a>Języki

Począwszy od wersji 2. x, środowisko uruchomieniowe używa modelu rozszerzalności języka, a wszystkie funkcje w aplikacji funkcji muszą korzystać z tego samego języka. Język funkcji w aplikacji funkcji jest wybierany podczas tworzenia aplikacji i jest przechowywany w ustawieniach [ \_ \_ środowiska wykonawczego procesu roboczego](functions-app-settings.md#functions_worker_runtime) . 

Poniższa tabela wskazuje, które języki programowania są obecnie obsługiwane w poszczególnych wersjach środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Uruchom w określonej wersji

Domyślnie aplikacje funkcji utworzone w Azure Portal i przez interfejs wiersza polecenia platformy Azure są ustawione na wersję 3. x. Tę wersję można zmienić w razie konieczności. Można zmienić wersję środowiska uruchomieniowego na 1. x po utworzeniu aplikacji funkcji, ale przed dodaniem jakichkolwiek funkcji.  Przechodzenie między 2. x i 3. x jest dozwolone nawet w przypadku aplikacji, które mają istniejące funkcje. Przed przeniesieniem aplikacji z istniejącymi funkcjami z 2. x do 3. x należy pamiętać o wszelkich [zmianach w zakresie od 2. x do 3. x](#breaking-changes-between-2x-and-3x). 

Przed wprowadzeniem zmiany w wersji głównej środowiska uruchomieniowego należy najpierw przetestować istniejący kod, wdrażając go w innej aplikacji funkcji uruchomionej w najnowszej wersji głównej. Ten test pomaga upewnić się, że działa prawidłowo po uaktualnieniu. 

Zmiany z wersji 3. x do v2. x nie są obsługiwane. Jeśli to możliwe, należy zawsze uruchamiać aplikacje w najnowszej obsługiwanej wersji środowiska uruchomieniowego funkcji. 

### <a name="changing-version-of-apps-in-azure"></a>Zmiana wersji aplikacji na platformie Azure

Wersja środowiska uruchomieniowego funkcji używanych przez opublikowane aplikacje na platformie Azure jest [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) określana przez ustawienie aplikacji. Obsługiwane są następujące główne wartości wersji środowiska uruchomieniowego:

| Wartość | Cel środowiska uruchomieniowego |
| ------ | -------- |
| `~3` | wersji |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Nie zmieniaj arbitralnie tego ustawienia, ponieważ mogą być wymagane inne zmiany ustawień aplikacji i zmiany w kodzie funkcji.

Aby dowiedzieć się więcej, zobacz [temat jak kierować Azure Functions wersjami środowiska uruchomieniowego](set-runtime-version.md).  

### <a name="pinning-to-a-specific-minor-version"></a>Przypinanie do określonej wersji pomocniczej

Aby rozwiązać problemy z aplikacją funkcji działającą w najnowszej wersji głównej, musisz przypiąć aplikację do określonej wersji pomocniczej. Zapewnia to czas, aby aplikacja działała prawidłowo w najnowszej wersji głównej. Sposób przypięcia do wersji pomocniczej różni się od systemów Windows i Linux. Aby dowiedzieć się więcej, zobacz [temat jak kierować Azure Functions wersjami środowiska uruchomieniowego](set-runtime-version.md).

Starsze wersje pomocnicze są okresowo usuwane z funkcji. Aby uzyskać najnowsze informacje dotyczące wydań Azure Functions, w tym usuwania konkretnych starszych wersji, należy monitorować [Azure App Service anonsów](https://github.com/Azure/app-service-announcements/issues). 

### <a name="pinning-to-version-20"></a>Przypinanie do wersji ~ 2,0

Aplikacje funkcji .NET uruchomione w wersji 2. x ( `~2` ) są automatycznie uaktualniane do uruchamiania na platformie .net core 3,1, która jest długoterminową wersją techniczną programu .NET Core 3. Uruchamianie funkcji .NET na platformie .NET Core 3,1 umożliwia korzystanie z najnowszych aktualizacji zabezpieczeń i ulepszeń produktów. 

Dowolna aplikacja funkcji przypięta do `~2.0` uruchamiania w programie .NET Core 2,2, która nie otrzymuje już zabezpieczeń i innych aktualizacji. Aby dowiedzieć się więcej, zobacz temat [funkcje w wersji 2. x](functions-dotnet-class-library.md#functions-v2x-considerations).   

## <a name="migrating-from-2x-to-3x"></a>Migrowanie z 2. x do 3. x

Azure Functions wersja 3. x jest wysoce wstecznie zgodna z wersją 2. x.  Wiele aplikacji powinno być w stanie bezpiecznie uaktualnić do 3. x bez wprowadzania żadnych zmian w kodzie.  Podczas przejścia do 3. x zaleca się uruchomienie obszernych testów przed zmianą wersji głównej w aplikacjach produkcyjnych.

### <a name="breaking-changes-between-2x-and-3x"></a>Istotne zmiany z przedziału od 2. x do 3. x

Poniżej znajdują się informacje o zmianach, które należy znać przed uaktualnieniem aplikacji 2. x do wersji 3. x.

#### <a name="javascript"></a>JavaScript

* Powiązania wyjściowe przypisane przez `context.done` lub wartości zwracane teraz zachowują się tak samo jak ustawienie w `context.bindings` .

* Obiekt wyzwalacza czasomierza jest camelCase zamiast PascalCase

* Funkcja wyzwalana przez centrum zdarzeń z `dataType` binarną będzie odbierać tablicę `binary` zamiast `string` .

* Nie można już uzyskać dostępu do ładunku żądania HTTP za pośrednictwem `context.bindingData.req` .  Nadal można uzyskać do niego dostęp jako parametr wejściowy, `context.req` i w `context.bindings` .

* Node.js 8 nie jest już obsługiwana i nie będzie działać w funkcjach 3. x.

#### <a name="net-core"></a>.NET Core

Główne różnice między wersjami w przypadku uruchamiania funkcji biblioteki klas .NET to środowisko uruchomieniowe platformy .NET Core. Funkcje w wersji 2. x są przeznaczone do uruchamiania w programie .NET Core 2,2, a wersja 3. x została zaprojektowana do uruchamiania na platformie .NET Core 3,1.  

* [Operacje serwera synchronicznego są domyślnie wyłączone](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

* Istotne zmiany wprowadzone przez platformę .NET Core w [wersji 3,1](/dotnet/core/compatibility/3.1) i w [wersji 3,0](/dotnet/core/compatibility/3.0), które nie są specyficzne dla funkcji, ale mogą nadal mieć wpływ na aplikację.

>[!NOTE]
>Ze względu na problemy z obsługą programu .NET Core 2,2, aplikacje funkcji przypięte do wersji 2 ( `~2` ) są zasadniczo uruchomione na platformie .NET Core 3,1. Aby dowiedzieć się więcej, zobacz temat [funkcje w wersji 2. x tryb zgodności](functions-dotnet-class-library.md#functions-v2x-considerations).

## <a name="migrating-from-1x-to-later-versions"></a>Migrowanie z 1. x do nowszych wersji

Możesz zdecydować się na migrację istniejącej aplikacji tak, aby korzystała z wersji 1. x środowiska uruchomieniowego w celu użycia nowszej wersji. Większość zmian, które należy wprowadzić, dotyczy zmian w środowisku uruchomieniowym języka, takich jak zmiany interfejsu API języka C# między .NET Framework 4,7 i .NET Core. Należy również upewnić się, że kod i biblioteki są zgodne z wybranym środowiskiem uruchomieniowym języka. Na koniec pamiętaj o uwzględnieniu wszelkich zmian w wyzwalaczu, powiązaniach i funkcjach wyróżnionych poniżej. W celu uzyskania najlepszych wyników migracji należy utworzyć nową aplikację funkcji w nowej wersji i przenieść istniejący kod funkcji w wersji 1. x do nowej aplikacji.  

Chociaż można przeprowadzić uaktualnienie "w miejscu", ręcznie aktualizując konfigurację aplikacji, przechodząc od 1. x do nowszej wersji, zawiera pewne istotne zmiany. Na przykład w języku C# obiekt debugowania jest zmieniany z `TraceWriter` na `ILogger` . Tworząc nowy projekt w wersji 3. x, rozpoczynasz pracę od zaktualizowanych funkcji w oparciu o najnowsze szablony w wersji 3. x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Zmiany w wyzwalaczach i powiązaniach po wersji 1. x

Począwszy od wersji 2. x, należy zainstalować rozszerzenia dla określonych wyzwalaczy i powiązań używanych przez funkcje w aplikacji. Jedyny wyjątek dla tych wyzwalaczy HTTP i Timer, które nie wymagają rozszerzenia.  Aby uzyskać więcej informacji, zobacz [Rejestrowanie i instalowanie rozszerzeń powiązań](./functions-bindings-register.md).

Istnieją także pewne zmiany w *function.js* lub atrybuty funkcji między wersjami. Na przykład właściwość centrum zdarzeń `path` jest teraz `eventHubName` . W [istniejącej tabeli powiązań](#bindings) znajdziesz linki do dokumentacji dla każdego powiązania.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Zmiany w funkcjach i funkcjach po wersji 1. x

Kilka funkcji zostało usuniętych, zaktualizowanych lub zastąpionych po wersji 1. x. Ta sekcja zawiera szczegółowe informacje o zmianach, które są widoczne w nowszych wersjach po użyciu wersji 1. x.

W wersji 2. x wprowadzono następujące zmiany:

* Klucze do wywoływania punktów końcowych HTTP są zawsze przechowywane w postaci zaszyfrowanej w usłudze Azure Blob Storage. W wersji 1. x klucze są domyślnie przechowywane w usłudze Azure File Storage. Podczas uaktualniania aplikacji z wersji 1. x do wersji 2. x istniejące klucze tajne w magazynie plików są resetowane.

* Środowisko uruchomieniowe w wersji 2. x nie obejmuje wbudowanej obsługi dostawców elementu webhook. Ta zmiana została wprowadzona w celu zwiększenia wydajności. Można nadal używać wyzwalaczy HTTP jako punktów końcowych dla elementów webhook.

* Plik konfiguracji hosta (host.json) powinien być pusty lub mieć ciąg `"version": "2.0"` .

* Aby poprawić monitorowanie, pulpit nawigacyjny zadań WebJob w portalu, który użył tego [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) Ustawienia, jest zastępowany Application Insights platformy Azure, która używa tego [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) Ustawienia. Aby uzyskać więcej informacji, zobacz [Monitor Azure Functions](functions-monitoring.md).

* Wszystkie funkcje w aplikacji funkcji muszą korzystać z tego samego języka. Podczas tworzenia aplikacji funkcji należy wybrać stos środowiska uruchomieniowego dla aplikacji. Stos środowiska uruchomieniowego jest określany przez [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) wartość w ustawieniach aplikacji. Ten wymóg został dodany w celu poprawy rozmiaru i czasu uruchamiania. Podczas programowania lokalnego należy również uwzględnić to ustawienie w [local.settings.jsna pliku](functions-run-local.md#local-settings-file).

* Domyślny limit czasu dla funkcji w planie App Service został zmieniony na 30 minut. Limit czasu można zmienić ręcznie na nieograniczony przy użyciu ustawienia [functionTimeout](functions-host-json.md#functiontimeout) w host.jsna.

* Ograniczenia współbieżności HTTP są implementowane domyślnie dla funkcji planu zużycia, z wartością domyślną 100 współbieżnych żądań na wystąpienie. Możesz zmienić to [`maxConcurrentRequests`](functions-host-json.md#http) ustawienie w host.jsna pliku.

* Ze względu na [ograniczenia programu .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), obsługa funkcji skryptów F # (FSX) została usunięta. Skompilowane funkcje języka F # (. FS) są nadal obsługiwane.

* Format adresu URL elementu webhook wyzwalacza Event Grid został zmieniony na `https://{app}/runtime/webhooks/{triggerName}` .

### <a name="locally-developed-application-versions"></a>Wersje aplikacji opracowane lokalnie

Możesz wprowadzić następujące aktualizacje, aby aplikacje funkcjonowały lokalnie w celu zmiany wersji.

#### <a name="visual-studio-runtime-versions"></a>Wersje środowiska uruchomieniowego programu Visual Studio

W programie Visual Studio podczas tworzenia projektu wybierana jest wersja środowiska uruchomieniowego. Narzędzia Azure Functions Tools for Visual Studio obsługują trzy główne wersje środowiska uruchomieniowego. Poprawna wersja jest używana podczas debugowania i publikowania na podstawie ustawień projektu. Ustawienia wersji są zdefiniowane w `.csproj` pliku w następujących właściwościach:

##### <a name="version-3x"></a>Wersja 3. x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x i .NET wymaga `Microsoft.NET.Sdk.Functions` co najmniej rozszerzenia `3.0.0` .

##### <a name="version-2x"></a>Wersja 2. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-1x"></a>Wersja 1. x

```xml
<TargetFramework>net472</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aktualizowanie aplikacji 2. x na 3. x w programie Visual Studio

Istnieje możliwość otwarcia istniejącej funkcji docelowej 2. x i przejścia do 3. x edytując `.csproj` plik i aktualizując powyższe wartości.  Program Visual Studio automatycznie zarządza wersjami środowiska uruchomieniowego na podstawie metadanych projektu.  Jednak jest to możliwe, jeśli nigdy nie utworzono aplikacji 3. x, a program Visual Studio nie ma jeszcze szablonów i środowiska uruchomieniowego dla 3. x na komputerze.  Może się to stać z błędem, takim jak "Brak dostępnego środowiska uruchomieniowego funkcji pasującego do wersji określonej w projekcie".  Aby pobrać najnowsze szablony i środowisko uruchomieniowe, przejdź przez środowisko, aby utworzyć nowy projekt funkcji.  Po wyświetleniu okna Wybieranie wersji i szablonu Zaczekaj, aż program Visual Studio zakończy pobieranie najnowszych szablonów. Po udostępnieniu i wyświetleniu najnowszych szablonów programu .NET Core 3 można uruchomić i debugować dowolny projekt skonfigurowany dla wersji 3. x.

> [!IMPORTANT]
> Funkcje w wersji 3. x można opracowywać tylko w programie Visual Studio, jeśli jest używany program Visual Studio w wersji 16,4 lub nowszej.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code i Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) jest używany do programowania w wierszu polecenia, a także przez [rozszerzenie Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) do Visual Studio Code. Aby opracowywać wersję 3. x, Zainstaluj wersję 3. x podstawowych narzędzi. Wersja 2. x programowanie wymaga wersji 2. x podstawowych narzędzi i tak dalej. Aby uzyskać więcej informacji, zobacz [instalowanie Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

Na potrzeby tworzenia Visual Studio Code może być również konieczne zaktualizowanie ustawienia użytkownika w `azureFunctions.projectRuntime` celu dopasowania do zainstalowanej wersji narzędzi.  To ustawienie powoduje także aktualizację szablonów i języków używanych podczas tworzenia aplikacji funkcji.  Aby można było tworzyć aplikacje w programie `~3` , należy zaktualizować `azureFunctions.projectRuntime` ustawienie użytkownika do programu `~3` .

![Ustawienie Azure Functions środowiska uruchomieniowego rozszerzenia](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplikacje Maven i Java

Aplikacje Java można migrować z wersji 2. x do 3. x przez [zainstalowanie wersji 3. x podstawowych narzędzi](functions-run-local.md#install-the-azure-functions-core-tools) wymaganych do lokalnego uruchamiania programu.  Po sprawdzeniu, czy aplikacja działa prawidłowo uruchomiona lokalnie w wersji 3. x, zaktualizuj plik aplikacji `POM.xml` , aby zmodyfikować `FUNCTIONS_EXTENSION_VERSION` ustawienie `~3` , jak w poniższym przykładzie:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Powiązania

Począwszy od wersji 2. x, środowisko uruchomieniowe używa nowego [modelu rozszerzalności powiązania](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , który oferuje następujące korzyści:

* Obsługa rozszerzeń powiązań innych firm.

* Rozdzielenie środowiska uruchomieniowego i powiązań. Ta zmiana umożliwia niezależne i wyłączanie rozszerzeń powiązań. Możesz na przykład wybrać opcję Uaktualnij do wersji rozszerzenia, która zależy od nowszej wersji bazowego zestawu SDK.

* Jaśniejsze środowisko wykonawcze, w którym tylko powiązania są znane i ładowane przez środowisko uruchomieniowe.

Z wyjątkiem wyzwalaczy HTTP i Timer, wszystkie powiązania muszą być jawnie dodane do projektu aplikacji funkcji lub zarejestrowane w portalu. Aby uzyskać więcej informacji, zobacz [Rejestrowanie rozszerzeń powiązań](./functions-bindings-expressions-patterns.md).

W poniższej tabeli przedstawiono powiązania obsługiwane w poszczególnych wersjach środowiska uruchomieniowego.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Kodowanie i testowanie usługi Azure Functions lokalnie](functions-run-local.md)
* [Jak docelowa Azure Functions wersje środowiska uruchomieniowego](set-runtime-version.md)
* [Informacje o wersji](https://github.com/Azure/azure-functions-host/releases)
