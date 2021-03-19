---
title: Wyzwalacz czasomierza dla Azure Functions
description: Dowiedz się, jak używać wyzwalaczy czasomierzy w Azure Functions.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f99f9d240a0a9220d3b7f57cddd0a4f8ba6b6101
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595995"
---
# <a name="timer-trigger-for-azure-functions"></a>Wyzwalacz czasomierza dla Azure Functions

W tym artykule opisano sposób pracy z wyzwalaczami czasomierza w Azure Functions. Wyzwalacz Timer pozwala uruchamiać funkcję zgodnie z harmonogramem.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Aby uzyskać informacje na temat ręcznego uruchamiania funkcji wyzwalanej przez czasomierz, zobacz [Ręczne uruchamianie funkcji niewyzwalanej przez protokół http](./functions-manually-run-non-http.md).

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Wyzwalacz czasomierza jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Wyzwalacz czasomierza jest dostępny w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która jest wykonywana za każdym razem, gdy minuty mają wartość widoczną przez pięć (np. Jeśli funkcja zaczyna się o 18:57:00, następna wydajność będzie równa 19:00:00). [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)Obiekt jest przesyłany do funkcji.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza czasomierza w *function.jsw* pliku i [funkcję skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy to wywołanie funkcji jest spowodowane pominiętym wystąpieniem harmonogramu. [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)Obiekt jest przesyłany do funkcji.

Oto dane powiązania w *function.js* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod skryptu w języku C#:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="java"></a>[Java](#tab/java)

Następująca przykładowa funkcja wyzwala i wykonuje co pięć minut. `@TimerTrigger`Adnotacja w funkcji definiuje harmonogram przy użyciu tego samego formatu ciągu co [cronus](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie wyzwalacza czasomierza w *function.jsw* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje dziennik wskazujący, czy to wywołanie funkcji jest spowodowane pominiętym wystąpieniem harmonogramu. [Obiekt Timer](#usage) jest przenoszona do funkcji.

Oto dane powiązania w *function.js* pliku:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Oto kod JavaScript:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład ilustruje sposób konfigurowania *function.jsna* i *run.ps1* pliku dla wyzwalacza czasomierza w programie [PowerShell](./functions-reference-powershell.md).

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

Wystąpienie [obiektu Timer](#usage) jest przesyłane jako pierwszy argument do funkcji.

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie jest stosowane powiązanie wyzwalacza czasomierza, którego konfiguracja została opisana w *function.js* pliku. Rzeczywista [funkcja języka Python](functions-reference-python.md) , która używa powiązania, jest opisana w pliku *__init__. PR* . Obiekt przesłany do funkcji jest [obiektem typu Azure. Functions. TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). Logika funkcji zapisuje w dziennikach wskazujący, czy bieżące wywołanie jest spowodowane pominiętym wystąpieniem harmonogramu.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Oto kod języka Python:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Konstruktor atrybutu przyjmuje wyrażenie typu CRONUS lub `TimeSpan` . Można używać `TimeSpan` tylko wtedy, gdy aplikacja funkcji jest uruchomiona w planie App Service. `TimeSpan` nie jest obsługiwane w przypadku funkcji użycia ani elastycznych wersji Premium.

Poniższy przykład przedstawia wyrażenie firmy CRONUS:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

`@TimerTrigger`Adnotacja w funkcji definiuje harmonogram przy użyciu tego samego formatu ciągu co [cronus](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `TimerTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiona na wartość "timerTrigger". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiona na wartość "in". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje obiekt timer w kodzie funkcji. | 
|**rozkład**|**ScheduleExpression**|[Wyrażenie CRONUS](#ncrontab-expressions) lub wartość [TimeSpan](#timespan) . Elementu `TimeSpan` można używać tylko w przypadku aplikacji funkcji działającej w planie App Service. Możesz umieścić wyrażenie harmonogramu w ustawieniu aplikacji i ustawić tę właściwość na nazwę ustawienia aplikacji ujętą w **%** znaki, jak w tym przykładzie: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Jeśli `true` , funkcja jest wywoływana po uruchomieniu środowiska uruchomieniowego. Na przykład środowisko uruchomieniowe jest uruchamiane, gdy aplikacja funkcji zostanie wznowiona po przejściu w stan bezczynności z powodu braku aktywności. gdy aplikacja funkcji zostanie ponownie uruchomiona z powodu zmiany funkcji i gdy aplikacja funkcji jest skalowana w dół. Tak więc **runOnStartup** powinna rzadko, jeśli kiedykolwiek jest ustawiona na `true` , szczególnie w środowisku produkcyjnym. |
|**useMonitor**|**UseMonitor**|Ustaw wartość `true` lub `false` , aby wskazać, czy harmonogram ma być monitorowany. Harmonogram monitorowania utrzymuje harmonogramy, aby pomóc w zapewnieniu, że harmonogram jest prawidłowo obsługiwany nawet po ponownym uruchomieniu wystąpień aplikacji funkcji. Jeśli nie ustawiono jawnie, wartość domyślna to `true` harmonogramy, które mają interwał cyklu większy lub równy 1 minuty. W przypadku harmonogramów, które wyzwalają więcej niż raz na minutę, wartość domyślna to `false` .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Zalecamy ustawienie **runOnStartup** na `true` w środowisku produkcyjnym. Użycie tego ustawienia sprawia, że kod jest wykonywany w bardzo nieprzewidywalnym czasie. W niektórych ustawieniach produkcyjnych te dodatkowe wykonania mogą spowodować znacznie wyższe koszty dla aplikacji hostowanych w planach zużycia. Na przykład po włączeniu **runOnStartup** wyzwalacz jest wywoływany za każdym razem, gdy aplikacja funkcji jest skalowana. Upewnij się, że w pełni zrozumiesz zachowanie środowiska produkcyjnego przed włączeniem **runOnStartup** w środowisku produkcyjnym.

## <a name="usage"></a>Użycie

Po wywołaniu funkcji wyzwalacza czasomierza obiekt Timer jest przenoszona do funkcji. Poniższy kod JSON to przykładowa reprezentacja obiektu Timer.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

`isPastDue`Właściwość jest, `true` gdy bieżące wywołanie funkcji jest późniejsze niż zaplanowana. Na przykład ponowne uruchomienie aplikacji funkcji może spowodować utratę wywołania.

## <a name="ncrontab-expressions"></a>Wyrażenia NCRONTAB

Azure Functions rozpoznaje wyrażenia NCRONTAB przy użyciu biblioteki [NCronTab](https://github.com/atifaziz/NCrontab) . Wyrażenie NCRONTAB jest podobne do wyrażenia CRONUS, z tą różnicą, że zawiera dodatkowe szóste pole na początku do użycia dla dokładności czasu w sekundach:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Każde pole może mieć jeden z następujących typów wartości:

|Typ  |Przykład  |Po wyzwoleniu  |
|---------|---------|---------|
|Określona wartość |<nobr>`0 5 * * * *`</nobr>| Co godzinę dnia o godz. 5 minut |
|Wszystkie wartości ( `*` )|<nobr>`0 * 5 * * *`</nobr>| Co minutę w ciągu godziny, rozpoczynając od godziny 5 |
|Zakres ( `-` operator)|<nobr>`5-7 * * * * *`</nobr>| Trzy razy minutę — w ciągu kilku sekund od 5 do 7 w każdej minucie każdej godziny każdego dnia |
|Zestaw wartości ( `,` operator)|<nobr>`5,8,10 * * * * *`</nobr>| Trzy razy minutę — w ciągu 5, 8 i 10 w każdej minucie każdej godziny każdego dnia |
|Wartość interwału ( `/` operator)|<nobr>`0 */5 * * * *`</nobr>| 12 razy godzinę — przy sekundzie 0 każdego piątego minuty każdej godziny każdego dnia |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Przykłady NCRONTAB

Poniżej przedstawiono kilka przykładów wyrażeń NCRONTAB, których można użyć dla wyzwalacza czasomierza w Azure Functions.

| Przykład            | Po wyzwoleniu                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | co pięć minut            |
| `0 0 * * * *`      | raz w górnej części co godzinę      |
| `0 0 */2 * * *`    | co dwie godziny               |
| `0 0 9-17 * * *`   | co godzinę od 9 do 5 PM  |
| `0 30 9 * * *`     | Codziennie o godzinie 9:30               |
| `0 30 9 * * 1-5`   | at 9:30 AM każdego dnia tygodnia           |
| `0 30 9 * Jan Mon` | o godzinie 9:30, co poniedziałek w styczniu |

> [!NOTE]
> Wyrażenie NCRONTAB wymaga **szóstego formatu pola** . Szósta pozycja pola jest wartością dla sekund, która jest umieszczana na początku wyrażenia. Na platformie Azure nie są obsługiwane pięć wyrażeń firmy CRONUS w polu.

### <a name="ncrontab-time-zones"></a>NCRONTAB strefy czasowe

Liczby w wyrażeniu firmy CRONUS odwołują się do daty i godziny, a nie przedziału czasu. Na przykład 5 w `hour` polu odnosi się do 5:00 am, nie co 5 godzin.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="timespan"></a>przedział_czasu

 Elementu `TimeSpan` można używać tylko w przypadku aplikacji funkcji działającej w planie App Service.

W przeciwieństwie do wyrażenia CRONUS, `TimeSpan` wartość określa przedział czasu między poszczególnymi wywołaniami funkcji. Gdy funkcja kończy działanie po upływie określonego interwału, czasomierz natychmiast wywołuje funkcję.

Jest to ciąg, który jest `TimeSpan` `hh:mm:ss` `hh` mniejszy niż 24. Gdy dwie pierwsze cyfry mają wartość 24 lub większą, format to `dd:hh:mm` . Oto kilka przykładów:

| Przykład      | Po wyzwoleniu |
|--------------|----------------|
| "01:00:00"   | co godzinę     |
| "00:01:00"   | co minutę   |
| "25:00:00"   | co 25 dni  |
| "1,00:00:00" | Codziennie      |

## <a name="scale-out"></a>Skalowanie w poziomie

Jeśli aplikacja funkcji jest skalowana do wielu wystąpień, tylko jedno wystąpienie funkcji wyzwalanej przez czasomierz jest uruchamiane we wszystkich wystąpieniach. Nie zostanie ono wyzwolone ponownie, jeśli wystąpi zaległe wywołanie.

## <a name="function-apps-sharing-storage"></a>Magazyn udostępniania aplikacji funkcji

W przypadku udostępniania kont magazynu w aplikacjach funkcji, które nie są wdrożone w usłudze App Service, może być konieczne jawne przypisanie identyfikatora hosta do poszczególnych aplikacji.

| Wersja funkcji | Ustawienie                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (i nowsze)  | `AzureFunctionsWebHost__hostid` Zmienna środowiskowa |
| 1.x               | `id` w *host.jsna*                                  |

Możesz pominąć wartość identyfikującą lub ręcznie ustawić konfigurację identyfikującą każdą aplikację funkcji na inną wartość.

Wyzwalacz czasomierza korzysta z blokady magazynu, aby upewnić się, że istnieje tylko jedno wystąpienie czasomierza, gdy aplikacja funkcji jest skalowana do wielu wystąpień. Jeśli dwie aplikacje funkcji mają tę samą konfigurację identyfikującą i każdy z nich używa wyzwalacza czasomierza, tylko jeden czasomierz jest uruchamiany.

## <a name="retry-behavior"></a>Zachowanie przy ponowieniu próby

W przeciwieństwie do wyzwalacza kolejki wyzwalacz czasomierza nie ponawia próby po awarii funkcji. Gdy funkcja nie powiedzie się, nie zostanie wywołana ponownie do następnego czasu zgodnie z harmonogramem.

## <a name="manually-invoke-a-timer-trigger"></a>Ręcznie Wywołaj wyzwalacz czasomierza

Wyzwalacz Timer dla Azure Functions zawiera element webhook protokołu HTTP, który można wywołać, aby ręcznie wyzwolić funkcję. Może to być bardzo przydatne w następujących scenariuszach.

* Testowanie integracji
* Zamiany gniazd w ramach testu dymu lub działania rozgrzewania
* Początkowe wdrożenie funkcji do natychmiastowego wypełnienia pamięci podręcznej lub tabeli odnośników w bazie danych

Zapoznaj się z [ręcznym uruchamianiem funkcji niewyzwalanej przez protokół http](./functions-manually-run-non-http.md) , aby uzyskać szczegółowe informacje na temat ręcznego wywoływania funkcji wyzwalanej przez czasomierz.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby uzyskać informacje o tym, co zrobić, gdy wyzwalacz czasomierza nie działa zgodnie z oczekiwaniami, zobacz temat [badanie i zgłaszanie problemów z wyzwalaczami wyzwalanymi przez funkcję Timer, które nie są wyzwalane](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przejdź do przewodnika Szybki Start korzystającego z wyzwalacza czasomierza](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
