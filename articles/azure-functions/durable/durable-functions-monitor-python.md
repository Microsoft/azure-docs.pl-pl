---
title: Monitory w Durable Functions (Python) — Azure
description: Dowiedz się, jak zaimplementować Monitor stanu przy użyciu rozszerzenia Durable Functions dla Azure Functions (Python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 9083821fa03c09949daaf3166367489248a4d7d2
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029190"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Scenariusz monitorowania w programie Durable Functions — przykład monitorowania problemu w usłudze GitHub

Wzorzec monitora odnosi się do elastycznego procesu cyklicznego w przepływie pracy — na przykład sondowania do momentu spełnienia określonych warunków. W tym artykule opisano przykład, który używa Durable Functions do wdrożenia monitorowania.

[! UWZGLĘDNIj trwałe funkcje — wymagania wstępne]

## <a name="scenario-overview"></a>Omówienie scenariusza

Ten przykład monitoruje liczbę problemów w repozytorium GitHub i ostrzega użytkownika, jeśli istnieje więcej niż 3 otwarte problemy. Można użyć zwykłej funkcji wyzwalanej czasomierza, aby zażądać liczby otwartych problemów w regularnych odstępach czasu. Jednak jeden problem z tym podejściem to **Zarządzanie okresem istnienia**. Jeśli należy wysłać tylko jeden alert, Monitor musi wyłączyć się po 3 lub większej liczbie problemów. Wzorzec monitorowania może zakończyć własne wykonywanie, między innymi korzyści:

* Monitory są uruchamiane w odstępach czasu, a nie harmonogramy: wyzwalacz czasomierza *jest uruchamiany* co godzinę; Monitor *czeka* jedną godzinę między akcjami. Akcje monitora nie nakładają się, chyba że zostanie określony, co może być ważne w przypadku długotrwałych zadań.
* Monitory mogą mieć dynamiczne interwały: czas oczekiwania można zmienić w zależności od pewnego warunku.
* Monitory mogą kończyć się, gdy jakiś warunek zostanie spełniony lub zostanie przerwany przez inny proces.
* Monitory mogą przyjmować parametry. W przykładzie pokazano, jak można zastosować ten sam proces monitorowania repozytorium do każdego żądanego publicznego repozytorium GitHub i numeru telefonu.
* Monitory są skalowalne. Ponieważ każdy monitor jest wystąpieniem aranżacji, można utworzyć wiele monitorów bez konieczności tworzenia nowych funkcji lub definiowania dalszych kodów.
* Monitory można łatwo zintegrować z większymi przepływami pracy. Monitor może być jedną sekcją bardziej złożonej funkcji aranżacji lub [aranżacją podrzędną](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Konfiguracja

### <a name="configuring-twilio-integration"></a>Konfigurowanie integracji Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E3_Monitor`: [Funkcja programu Orchestrator](durable-functions-bindings.md#orchestration-trigger) , która `E3_TooManyOpenIssues` okresowo wywołuje. Wywołuje, `E3_SendAlert` czy wartość zwracana `E3_TooManyOpenIssues` jest `True` .
* `E3_TooManyOpenIssues`: [Funkcja działania](durable-functions-bindings.md#activity-trigger) , która sprawdza, czy repozytorium ma zbyt wiele otwartych problemów. W celach demonstracyjnych Rozważmy posiadanie więcej niż 3 otwartych problemów zbyt wiele.
* `E3_SendAlert`: Funkcja działania, która wysyła wiadomość SMS za pośrednictwem Twilio.

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor funkcja programu Orchestrator

# <a name="python"></a>[Python](#tab/python)

Funkcja **E3_Monitor** używa standardowego *function.jsna* potrzeby funkcji programu Orchestrator.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Oto kod implementujący funkcję:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]

---

Ta funkcja programu Orchestrator wykonuje następujące akcje:

1. Pobiera *repozytorium* do monitorowania i *numer telefonu* , do którego wyśle powiadomienie SMS.
2. Określa czas wygaśnięcia monitora. Przykład używa zakodowanej wartości zwięzłości.
3. Wywołuje **E3_TooManyOpenIssues** , aby określić, czy w żądanym repozytorium występuje zbyt wiele otwartych problemów.
4. Jeśli wystąpi zbyt wiele problemów, program wywołuje **E3_SendAlert** , aby wysłać powiadomienie SMS do żądanego numeru telefonu.
5. Tworzy trwały czasomierz w celu wznowienia aranżacji przy następnym interwale sondowania. Przykład używa zakodowanej wartości zwięzłości.
6. Kontynuuje działanie do momentu upływu czasu wygaśnięcia bieżącego monitora lub wysłania alertu programu SMS.

Jednocześnie można uruchamiać wiele wystąpień programu Orchestrator, wywołując funkcję programu Orchestrator wiele razy. Repozytorium do monitorowania i numer telefonu, do którego można określić alert programu SMS. Na koniec należy pamiętać, że funkcja programu Orchestrator *nie* jest uruchomiona podczas oczekiwania na czasomierz, więc opłata nie zostanie naliczona.


### <a name="e3_toomanyopenissues-activity-function"></a>Funkcja działania E3_TooManyOpenIssues

Podobnie jak w przypadku innych próbek, funkcje działania pomocnika są regularnymi funkcjami, które używają `activityTrigger` powiązania wyzwalacza. Funkcja **E3_TooManyOpenIssues** pobiera listę obecnie otwartych problemów w repozytorium i określa, czy jest "zbyt wiele" z nich: więcej niż 3 zgodnie z naszym przykładem.

# <a name="python"></a>[Python](#tab/python)

*function.json* został zdefiniowany w następujący sposób:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

A oto implementacja.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]

---

### <a name="e3_sendalert-activity-function"></a>Funkcja działania E3_SendAlert

Funkcja **E3_SendAlert** używa powiązania Twilio do wysyłania wiadomości SMS z powiadomieniem użytkownika końcowego o istnieniu co najmniej 3 otwartych problemów oczekujących na rozwiązanie.

# <a name="python"></a>[Python](#tab/python)

*function.json* jest prosty:

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

A Oto kod, który wysyła wiadomość SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Będziesz potrzebować konta usługi [GitHub](https://github.com/) . W tym celu Utwórz tymczasowe repozytorium publiczne, do którego można otworzyć problemy.

Korzystając z funkcji wyzwalanych przez protokół HTTP zawartych w przykładzie, można uruchomić aranżację, wysyłając następujące żądanie HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Na przykład jeśli Twoja nazwa użytkownika usługi GitHub to, `foo` a Twoje repozytorium jest `bar` wartością dla, `"repo"` należy `"foo/bar"` .

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

Wystąpienie **E3_Monitor** uruchamia i wysyła zapytanie do podanego repozytorium pod kątem otwartych problemów. W przypadku znalezienia co najmniej 3 otwartych problemów wywołuje funkcję działania w celu wysłania alertu; w przeciwnym razie ustawia czasomierz. Gdy czasomierz wygaśnie, aranżacja zostanie wznowiona.

Działanie aranżacji można zobaczyć, przeglądając dzienniki funkcji w portalu Azure Functions.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

Aranżacja zostanie zakończona po osiągnięciu limitu czasu lub wykryciu więcej niż 3 otwarte problemy. Można również użyć `terminate` interfejsu API wewnątrz innej funkcji lub wywołać element webhook **terminatePostUri** http, do którego odwołuje się powyżej powyższej odpowiedzi 202. Aby użyć elementu webhook, Zastąp `{text}` z przyczyną wczesnego zakończenia. Adres URL POST protokołu HTTP będzie wyglądać w następujący sposób:

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje, jak używać Durable Functions do monitorowania stanu źródła zewnętrznego przy użyciu [trwałych czasomierzy](durable-functions-timers.md) i logiki warunkowej. Następny przykład pokazuje, jak używać zdarzeń zewnętrznych i [trwałych czasomierzy](durable-functions-timers.md) do obsługi interakcji człowieka.

> [!div class="nextstepaction"]
> [Uruchamianie przykładu interakcji człowieka](durable-functions-phone-verification.md)
