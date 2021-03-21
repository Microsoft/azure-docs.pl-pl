---
title: Skonfiguruj monitorowanie dla Azure Functions
description: Dowiedz się, jak połączyć aplikację funkcji, aby Application Insights do monitorowania i jak skonfigurować zbieranie danych.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5007009d9aabf9a1c1c6e1d5c2f286c0ba25b340
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493757"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Jak skonfigurować monitorowanie dla Azure Functions

Azure Functions integruje się z Application Insights, aby lepiej monitorować aplikacje funkcji. Application Insights, funkcja Azure Monitor, to rozszerzalna usługa zarządzania wydajnością aplikacji (APM), która zbiera dane wygenerowane przez aplikację funkcji, w tym informacje o zapisach aplikacji do dzienników. Integracja Application Insights jest zazwyczaj włączana podczas tworzenia aplikacji funkcji. Jeśli aplikacja nie ma zestawu kluczy instrumentacji, należy najpierw [włączyć integrację Application Insights](#enable-application-insights-integration). 

Application Insights można używać bez żadnej konfiguracji niestandardowej. Konfiguracja domyślna może powodować duże ilości danych. W przypadku korzystania z subskrypcji programu Visual Studio Azure można osiągnąć limit danych Application Insights. Aby dowiedzieć się więcej o kosztach Application Insights, zobacz [Zarządzanie użyciem i kosztami Application Insights](../azure-monitor/app/pricing.md).

W dalszej części tego artykułu dowiesz się, jak skonfigurować i dostosować dane wysyłane przez funkcje do Application Insights. W przypadku aplikacji funkcji rejestrowanie jest konfigurowane w [host.js] pliku. 

> [!NOTE]
> Można użyć specjalnie skonfigurowanych ustawień aplikacji do reprezentowania określonych ustawień w host.jspliku dla określonego środowiska. Pozwala to efektywnie zmieniać host.jsw ustawieniach bez konieczności ponownego publikowania host.jsw pliku w projekcie. Aby dowiedzieć się więcej, zobacz [Zastąp host.jswartościami](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Konfiguruj kategorie

Rejestrator Azure Functions obejmuje *kategorię* dla każdego dziennika. Kategoria wskazuje, która część kodu środowiska uruchomieniowego lub kod funkcji zapisał dziennik. Kategorie różnią się w zależności od wersji 1. x i nowszych. Poniższy wykres zawiera opis głównych kategorii dzienników tworzonych przez środowisko uruchomieniowe. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

| Kategoria | Tabela | Opis |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **tamten**| Dane zależności są automatycznie zbierane dla niektórych usług. W przypadku pomyślnych uruchomień te dzienniki znajdują się na `Information` poziomie. Aby dowiedzieć się więcej, zobacz [zależności](functions-monitoring.md#dependencies). Wyjątki są rejestrowane na `Error` poziomie. Środowisko uruchomieniowe tworzy również `Warning` dzienniki poziomu, takie jak, gdy komunikaty kolejki są wysyłane do [kolejki trującej](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Zestawy SDK języka C# i JavaScript pozwalają zbierać niestandardowe metryki i rejestrować zdarzenia niestandardowe. Aby dowiedzieć się więcej, zobacz [niestandardowe dane telemetryczne](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **ścieżki**| Obejmuje funkcję rozpoczętą i kompletną dzienników dla określonych uruchomień funkcji. W przypadku pomyślnych uruchomień te dzienniki znajdują się na `Information` poziomie. Wyjątki są rejestrowane na `Error` poziomie. Środowisko uruchomieniowe tworzy również `Warning` dzienniki poziomu, takie jak, gdy komunikaty kolejki są wysyłane do [kolejki trującej](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **ścieżki**| Dzienniki generowane przez użytkownika, które mogą mieć dowolny poziom dziennika. Aby dowiedzieć się więcej na temat zapisywania do dzienników z funkcji, zobacz [Zapisywanie w dziennikach](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Te dzienniki generowane przez środowisko uruchomieniowe zapewniają liczniki i średnie wywołania funkcji w [konfigurowalnym](#configure-the-aggregator) okresie. Domyślny okres to 30 sekund lub 1 000 wyników, zależnie od tego, co nastąpi wcześniej. Przykłady to liczba przebiegów, Częstotliwość powodzeń i czas trwania. Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na poziomie `Warning` lub wyższym nie będą widoczne żadne z tych danych. |
| **`Host.Results`** | **żądania** | Te dzienniki generowane przez środowisko uruchomieniowe wskazują na powodzenie lub Niepowodzenie funkcji. Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na poziomie `Warning` lub wyższym nie będą widoczne żadne z tych danych. |
| **`Microsoft`** | **ścieżki** | W pełni kwalifikowana Kategoria dziennika, która odzwierciedla składnik środowiska uruchomieniowego .NET wywoływany przez hosta.  |
| **`Worker`** | **ścieżki** | Dzienniki wygenerowane przez proces roboczy języka dla języków non-.NET. Dzienniki procesów roboczych języka mogą być również rejestrowane w `Microsoft.*` kategorii, na przykład `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher` . Te dzienniki są zapisywane na `Information` poziomie.|

> [!NOTE]
> W przypadku funkcji biblioteki klas platformy .NET założono, że używasz, `ILogger` a nie `ILogger<T>` . Aby dowiedzieć się więcej, zobacz [dokumentację funkcji ILogger](functions-dotnet-class-library.md#ilogger). 

# <a name="v1x"></a>[V1. x](#tab/v1)

| Kategoria | Tabela | Opis |
| ----- | ----- | ----- |
| **`Function`** | **ścieżki**| Dzienniki generowane przez użytkownika, które mogą mieć dowolny poziom dziennika. Aby dowiedzieć się więcej na temat zapisywania do dzienników z funkcji, zobacz [Zapisywanie w dziennikach](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Te dzienniki generowane przez środowisko uruchomieniowe zapewniają liczniki i średnie wywołania funkcji w [konfigurowalnym](#configure-the-aggregator) okresie. Domyślny okres to 30 sekund lub 1 000 wyników, zależnie od tego, co nastąpi wcześniej. Przykłady to liczba przebiegów, Częstotliwość powodzeń i czas trwania. Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na poziomie `Warning` lub wyższym nie będą widoczne żadne z tych danych. |
| **`Host.Executor`** | **ścieżki** | Obejmuje **funkcję uruchomioną** i **wykonane dzienniki funkcji** dla określonych uruchomień funkcji. W przypadku pomyślnych uruchomień te dzienniki są `Information` poziomami. Wyjątki są rejestrowane na `Error` poziomie. Środowisko uruchomieniowe tworzy również `Warning` dzienniki poziomu, takie jak, gdy komunikaty kolejki są wysyłane do [kolejki trującej](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **żądania** | Te dzienniki generowane przez środowisko uruchomieniowe wskazują na powodzenie lub Niepowodzenie funkcji. Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na poziomie `Warning` lub wyższym nie będą widoczne żadne z tych danych. |

---

Kolumna **tabela** wskazuje, w której tabeli Application Insights dziennik jest zapisywana. 

## <a name="configure-log-levels"></a>Konfigurowanie poziomów dziennika

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Dla każdej kategorii należy określić minimalny poziom rejestrowania do wysłania. host.jsustawienia różnią się w zależności od [wersji środowiska uruchomieniowego funkcji](functions-versions.md). 

W poniższym przykładzie zdefiniowano rejestrowanie na podstawie następujących reguł:

+ W przypadku dzienników `Host.Results` lub `Function` należy rejestrować tylko zdarzenia na `Error` poziomie lub wyższym. 
+ W przypadku dzienników `Host.Aggregator` dziennika należy rejestrować wszystkie wygenerowane metryki ( `Trace` ).
+ W przypadku wszystkich innych dzienników, w tym dzienników użytkowników, należy rejestrować tylko `Information` zdarzenia poziomu i wyższe.

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[V1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Jeśli [host.json] zawiera wiele dzienników, które zaczynają się od tego samego ciągu, w pierwszej kolejności są dopasowywane dzienniki. Rozważmy następujący przykład, który rejestruje wszystko w środowisku uruchomieniowym, z wyjątkiem tego `Host.Aggregator` , na `Error` poziomie:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[V1. x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

Możesz użyć ustawienia poziomu dziennika, `None` Aby uniemożliwić zapisywanie jakichkolwiek dzienników dla kategorii. 

## <a name="configure-the-aggregator"></a>Konfigurowanie agregatora

Jak wskazano w poprzedniej sekcji, środowisko uruchomieniowe agreguje dane dotyczące wykonywania funkcji w danym okresie czasu. Domyślny okres to 30 sekund lub 1 000 uruchomienia, w zależności od tego, co nastąpi wcześniej. To ustawienie można skonfigurować w [host.js] pliku.  Oto przykład:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfiguruj próbkowanie

Application Insights zawiera funkcję [próbkowania](../azure-monitor/app/sampling.md) , która umożliwia ochronę przed generowaniem zbyt dużej ilości danych telemetrycznych w przypadku zakończonych wykonań w czasie szczytowego ładowania. Gdy częstotliwość wykonywania przychodzących przekracza określony próg, Application Insights zaczyna losowo ignorować niektóre wykonania przychodzące. Domyślne ustawienie maksymalnej liczby wykonań na sekundę to 20 (pięć w wersji 1. x). Można skonfigurować próbkowanie w [host.jsna](./functions-host-json.md#applicationinsights).  Oto przykład:

# <a name="v2x"></a>[v2. x +](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Niektóre typy danych telemetrycznych można wykluczać z próbkowania. W tym przykładzie dane typu `Request` i `Exception` są wykluczone z próbkowania. Daje to pewność, że *wszystkie* wykonania funkcji (żądania) i wyjątki są rejestrowane, podczas gdy inne typy telemetrii nadal podlegają próbkowaniu. 

# <a name="v1x"></a>[V1. x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Aby dowiedzieć się więcej, zobacz [próbkowanie w Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Konfigurowanie dzienników kontrolera skalowania

_Ta funkcja jest w wersji zapoznawczej._ 

[Kontroler skalowania Azure Functions](./event-driven-scaling.md#runtime-scaling) może wysyłać dzienniki do Application Insights lub do magazynu obiektów blob, aby lepiej zrozumieć decyzje podejmowane przez kontroler skalowania dla aplikacji funkcji.

Aby włączyć tę funkcję, należy dodać ustawienie aplikacji o nazwie `SCALE_CONTROLLER_LOGGING_ENABLED` do ustawień aplikacji funkcji. Wartość tego ustawienia musi być w formacie `<DESTINATION>:<VERBOSITY>` , w oparciu o następujące:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Na przykład następujące polecenie interfejsu wiersza polecenia platformy Azure włącza pełne rejestrowanie w kontrolerze skalowania do Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

W tym przykładzie Zastąp `<FUNCTION_APP_NAME>` `<RESOURCE_GROUP_NAME>` wartości i nazwą aplikacji funkcji i nazwą grupy zasobów odpowiednio. 

Następujące polecenie interfejsu wiersza polecenia platformy Azure wyłącza rejestrowanie przez ustawienie szczegółowości `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Możesz również wyłączyć rejestrowanie, usuwając `SCALE_CONTROLLER_LOGGING_ENABLED` ustawienie za pomocą następującego polecenia interfejsu wiersza poleceń platformy Azure:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

Po włączeniu rejestrowania kontrolera skalowania można teraz [wykonywać zapytania dotyczące dzienników kontrolera skalowania](analyze-telemetry-data.md#query-scale-controller-logs). 

## <a name="enable-application-insights-integration"></a>Enable Application Insights integration (Włączanie integracji z usługą Application Insights)

Aby aplikacja funkcji mogła wysyłać dane do Application Insights, musi znać klucz Instrumentacji zasobu Application Insights. Klucz musi być w ustawieniu aplikacji o nazwie **APPINSIGHTS_INSTRUMENTATIONKEY**.

Podczas tworzenia aplikacji funkcji [w Azure Portal](./functions-get-started.md)z wiersza polecenia przy użyciu [Azure Functions Core Tools](./create-first-function-cli-csharp.md)lub przy użyciu [Visual Studio Code](./create-first-function-vs-code-csharp.md)integracja Application Insights jest włączona domyślnie. Zasób Application Insights ma taką samą nazwę jak aplikacja funkcji i jest tworzony w tym samym regionie lub w najbliższym regionie.

### <a name="new-function-app-in-the-portal"></a>Nowa aplikacja funkcji w portalu

Aby przejrzeć tworzony zasób Application Insights, zaznacz go, aby rozwinąć okno **Application Insights** . **Nową nazwę zasobu** można zmienić lub wybrać inną **lokalizację w lokalizacji** [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w której mają być przechowywane dane.

![Włącz Application Insights podczas tworzenia aplikacji funkcji](media/functions-monitoring/enable-ai-new-function-app.png)

Po wybraniu opcji **Utwórz** zasób Application Insights zostanie utworzony za pomocą aplikacji funkcji, która ma `APPINSIGHTS_INSTRUMENTATIONKEY` ustawioną wartość ustawienia aplikacji. Wszystko jest gotowe do użycia.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Dodawanie do istniejącej aplikacji funkcji 

Jeśli zasób Application Insights nie został utworzony w aplikacji funkcji, wykonaj następujące kroki, aby utworzyć zasób. Następnie można dodać klucz Instrumentacji z tego zasobu jako [Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) w aplikacji funkcji.

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **aplikacja funkcji**, a następnie wybierz aplikację funkcji. 

1. Wybierz pozycję **Application Insights nie jest skonfigurowany** transparent w górnej części okna. Jeśli nie widzisz tego transparentu, Twoja aplikacja może mieć już włączony Application Insights.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Włączanie Application Insights z poziomu portalu":::

1. Rozwiń węzeł **Zmień zasób** i utwórz zasób Application Insights przy użyciu ustawień określonych w poniższej tabeli.  

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nowa nazwa zasobu** | Unikatowa nazwa aplikacji | Najłatwiej używać tej samej nazwy, co aplikacja funkcji, która musi być unikatowa w subskrypcji. | 
    | **Lokalizacja** | West Europe | Jeśli to możliwe, użyj tego samego [regionu](https://azure.microsoft.com/regions/) , w którym znajduje się aplikacja funkcji lub która znajduje się blisko tego regionu. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Tworzenie zasobu Application Insights":::

1. Wybierz przycisk **Zastosuj**. 

   Zasób Application Insights jest tworzony w tej samej grupie zasobów i subskrypcji co aplikacja funkcji. Po utworzeniu zasobu Zamknij okno Application Insights.

1. W aplikacji funkcji wybierz pozycję **Konfiguracja** w obszarze **Ustawienia**, a następnie wybierz pozycję **Ustawienia aplikacji**. Jeśli zobaczysz ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY` , Application Insights integracja jest włączona dla aplikacji funkcji działającej na platformie Azure. Jeśli z jakiegoś powodu to ustawienie nie istnieje, Dodaj je za pomocą klucza Instrumentacji Application Insights jako wartość.

> [!NOTE]
> Wczesne wersje funkcji używały wbudowanego monitorowania, które nie jest już zalecane. Podczas włączania integracji Application Insights dla takiej aplikacji funkcji należy również [wyłączyć wbudowane funkcje rejestrowania](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Wyłączanie wbudowanego rejestrowania

Po włączeniu Application Insights Wyłącz wbudowane rejestrowanie korzystające z usługi Azure Storage. Wbudowane rejestrowanie jest przydatne do testowania z lekkimi obciążeniami, ale nie jest przeznaczone do użycia w środowisku produkcyjnym. W celu monitorowania produkcji zalecamy Application Insights. Jeśli Wbudowana funkcja rejestrowania jest używana w środowisku produkcyjnym, rekord rejestrowania może być niekompletny z powodu ograniczania w usłudze Azure Storage.

Aby wyłączyć wbudowane rejestrowanie, Usuń `AzureWebJobsDashboard` ustawienie aplikacji. Aby uzyskać informacje dotyczące sposobu usuwania ustawień aplikacji w Azure Portal, zobacz sekcję **Ustawienia aplikacji** w temacie [jak zarządzać aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md#settings). Przed usunięciem ustawienia aplikacji upewnij się, że żadne funkcje w tej samej aplikacji funkcji nie używają ustawienia dla wyzwalaczy lub powiązań usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu, zobacz:

+ [Monitorowanie usługi Azure Functions](functions-monitoring.md)
+ [Analizowanie Azure Functions danych telemetrycznych w Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.jsna]: functions-host-json.md
