---
title: Monitory w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować Monitor stanu przy użyciu rozszerzenia Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ed92156df9d8e1e07b56cea4b1e64edee11d68d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77562126"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scenariusz monitorowania w przykładowym monitorze Durable Functions-Pogoda

Wzorzec monitora odnosi się do elastycznego procesu *cyklicznego* w przepływie pracy — na przykład sondowania do momentu spełnienia określonych warunków. W tym artykule opisano przykład, który używa [Durable Functions](durable-functions-overview.md) do wdrożenia monitorowania.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Ten przykład służy do monitorowania bieżących warunków pogodowych lokalizacji i powiadamiania użytkownika przez program SMS, gdy Skies są jasne. Aby sprawdzić Pogoda i wysyłać alerty, można użyć zwykłej funkcji wyzwalanej przez czasomierz. Jednak jeden problem z tym podejściem to **Zarządzanie okresem istnienia**. Jeśli należy wysłać tylko jeden alert, Monitor musi wyłączyć się po wykryciu jasnej pogody. Wzorzec monitorowania może zakończyć własne wykonywanie, między innymi korzyści:

* Monitory są uruchamiane w odstępach czasu, a nie harmonogramy: wyzwalacz czasomierza *jest uruchamiany* co godzinę; Monitor *czeka* jedną godzinę między akcjami. Akcje monitora nie nakładają się, chyba że zostanie określony, co może być ważne w przypadku długotrwałych zadań.
* Monitory mogą mieć dynamiczne interwały: czas oczekiwania można zmienić w zależności od pewnego warunku.
* Monitory mogą kończyć się, gdy jakiś warunek zostanie spełniony lub zostanie przerwany przez inny proces.
* Monitory mogą przyjmować parametry. W przykładzie pokazano, jak ten sam proces monitorowania pogody można zastosować do każdej żądanej lokalizacji i numeru telefonu.
* Monitory są skalowalne. Ponieważ każdy monitor jest wystąpieniem aranżacji, można utworzyć wiele monitorów bez konieczności tworzenia nowych funkcji lub definiowania dalszych kodów.
* Monitory można łatwo zintegrować z większymi przepływami pracy. Monitor może być jedną sekcją bardziej złożonej funkcji aranżacji lub [aranżacją podrzędną](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfiguracja

### <a name="configuring-twilio-integration"></a>Konfigurowanie integracji Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>Konfigurowanie integracji podziemnej pogody

Ten przykład obejmuje użycie podziemnego interfejsu API Pogoda do sprawdzenia bieżących warunków pogodowych dla lokalizacji.

Pierwszym z nich jest konto podziemne. Możesz go utworzyć bezpłatnie o [https://www.wunderground.com/signup](https://www.wunderground.com/signup) . Po utworzeniu konta należy uzyskać klucz interfejsu API. Możesz to zrobić [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) , odwiedzając, a następnie wybierając pozycję Ustawienia klucza. Plan dewelopera Stratus jest bezpłatny i wystarczający do uruchomienia tego przykładu.

Gdy masz klucz interfejsu API, Dodaj następujące **ustawienie aplikacji** do aplikacji funkcji.

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **WeatherUndergroundApiKey**  | Klucz interfejsu API sieci podziemnej Pogoda. |

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E3_Monitor`: [Funkcja programu Orchestrator](durable-functions-bindings.md#orchestration-trigger) , która `E3_GetIsClear` okresowo wywołuje. Jest wywoływana, `E3_SendGoodWeatherAlert` Jeśli `E3_GetIsClear` zwraca wartość true.
* `E3_GetIsClear`: [Funkcja działania](durable-functions-bindings.md#activity-trigger) , która sprawdza bieżące warunki pogodowe dla lokalizacji.
* `E3_SendGoodWeatherAlert`: Funkcja działania, która wysyła wiadomość SMS za pośrednictwem Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor funkcja programu Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Usługa Orchestrator wymaga lokalizacji do monitorowania i numeru telefonu, aby wysłać komunikat do momentu, gdy zostanie on wyczyszczony w lokalizacji. Te dane są przesyłane do programu Orchestrator jako obiekt silnie określony `MonitorRequest` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funkcja **E3_Monitor** używa standardowego *function.jsna* potrzeby funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

Oto kod implementujący funkcję:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

---

Ta funkcja programu Orchestrator wykonuje następujące akcje:

1. Pobiera **MonitorRequest** składa się z *lokalizacji* do monitorowania oraz *numeru telefonu* , do którego wyśle powiadomienie SMS.
2. Określa czas wygaśnięcia monitora. Przykład używa zakodowanej wartości zwięzłości.
3. Wywołuje **E3_GetIsClear** , aby określić, czy w żądanym miejscu znajdują się jasne Skies.
4. Jeśli pogoda jest jasne, program wywołuje **E3_SendGoodWeatherAlert** , aby wysłać powiadomienie SMS do żądanego numeru telefonu.
5. Tworzy trwały czasomierz w celu wznowienia aranżacji przy następnym interwale sondowania. Przykład używa zakodowanej wartości zwięzłości.
6. Kontynuuje działanie do momentu upływu czasu wygaśnięcia bieżącego monitora lub wysłania alertu programu SMS.

Jednocześnie można uruchamiać wiele wystąpień programu Orchestrator, wywołując funkcję programu Orchestrator wiele razy. Można określić lokalizację do monitorowania i numer telefonu, na który ma zostać wysłany alert programu SMS.

### <a name="e3_getisclear-activity-function"></a>Funkcja działania E3_GetIsClear

Podobnie jak w przypadku innych próbek, funkcje działania pomocnika są regularnymi funkcjami, które używają `activityTrigger` powiązania wyzwalacza. Funkcja **E3_GetIsClear** pobiera bieżące warunki pogodowe przy użyciu podziemnego interfejsu API Pogoda i określa, czy ta wartość jest wyczyszczona.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* został zdefiniowany w następujący sposób:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

A oto implementacja.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>Funkcja działania E3_SendGoodWeatherAlert

Funkcja **E3_SendGoodWeatherAlert** używa powiązania Twilio do wysyłania wiadomości SMS z powiadomieniem użytkownika końcowego, że jest to dobry czas na przeprowadzenie.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> Musisz zainstalować `Microsoft.Azure.WebJobs.Extensions.Twilio` pakiet NuGet, aby uruchomić przykładowy kod.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* jest prosty:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

A Oto kod, który wysyła wiadomość SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Korzystając z funkcji wyzwalanych przez protokół HTTP zawartych w przykładzie, można uruchomić aranżację, wysyłając następujące żądanie HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Wystąpienie **E3_Monitor** uruchamia i bada bieżące warunki pogodowe dla żądanej lokalizacji. Jeśli pogoda jest jasne, wywołuje funkcję działania w celu wysłania alertu; w przeciwnym razie ustawia czasomierz. Gdy czasomierz wygaśnie, aranżacja zostanie wznowiona.

Działanie aranżacji można zobaczyć, przeglądając dzienniki funkcji w portalu Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

Aranżacja zostanie [zakończona](durable-functions-instance-management.md) po osiągnięciu limitu czasu lub wykryciu Skies. Można również użyć `TerminateAsync` (.NET) lub `terminate` (JavaScript) wewnątrz innej funkcji lub wywołać element webhook **terminatePostUri** http, do którego odwołuje się powyżej 202 powyżej, zastępując `{text}` z powód zakończenia:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje, jak używać Durable Functions do monitorowania stanu źródła zewnętrznego przy użyciu [trwałych czasomierzy](durable-functions-timers.md) i logiki warunkowej. Następny przykład pokazuje, jak używać zdarzeń zewnętrznych i [trwałych czasomierzy](durable-functions-timers.md) do obsługi interakcji człowieka.

> [!div class="nextstepaction"]
> [Uruchamianie przykładu interakcji człowieka](durable-functions-phone-verification.md)
