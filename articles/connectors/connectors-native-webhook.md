---
title: Zaczekaj i Odpowiedz na zdarzenia
description: Automatyzowanie przepływów pracy wyzwalających, wstrzymuje i wznawiane na podstawie zdarzeń w punkcie końcowym usługi przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80656277"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Tworzenie i uruchamianie zautomatyzowanych przepływów zadań opartych na zdarzeniach za pomocą elementów webhook protokołu HTTP w Azure Logic Apps

Dzięki [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanemu łącznikowi elementu WEBHOOK protokołu HTTP można zautomatyzować przepływy pracy, które oczekują i uruchamiają się w oparciu o określone zdarzenia, które wystąpiły w punkcie końcowym http lub HTTPS przez tworzenie aplikacji logiki. Można na przykład utworzyć aplikację logiki, która monitoruje punkt końcowy usługi przez oczekiwanie na określone zdarzenie przed wyzwoleniem przepływu pracy i uruchomieniem określonych akcji zamiast regularnego sprawdzania lub *sondowania* tego punktu końcowego.

Oto przykładowe przepływy pracy oparte na zdarzeniach:

* Poczekaj na dostarczenie elementu z [centrum zdarzeń platformy Azure](https://github.com/logicappsio/EventHubAPI) przed wyzwoleniem uruchomienia aplikacji logiki.
* Poczekaj na zatwierdzenie przed kontynuowaniem przepływu pracy.

## <a name="how-do-webhooks-work"></a>Jak działają elementy webhook?

Wyzwalacz elementu webhook protokołu HTTP to oparte na zdarzeniach, które nie zależą od regularnego sprawdzania i sondowania dla nowych elementów. Po zapisaniu aplikacji logiki, która rozpoczyna się od wyzwalacza elementu webhook lub zmiany aplikacji logiki z wyłączone na włączone, wyzwalacz elementu webhook *subskrybuje* określoną usługę lub punkt końcowy przez zarejestrowanie *adresu URL wywołania zwrotnego* z tą usługą lub punktem końcowym. Wyzwalacz czeka, aż usługa lub punkt końcowy wywoła adres URL, co spowoduje uruchomienie aplikacji logiki. Podobnie jak w przypadku [wyzwalacza żądania](connectors-native-reqres.md)aplikacja logiki jest uruchamiana natychmiast po wystąpieniu określonego zdarzenia. Wyzwalacz *anulował subskrypcję* usługi lub punktu końcowego w przypadku usunięcia wyzwalacza i zapisania aplikacji logiki lub zmiany aplikacji logiki z włączone na wyłączone.

Akcja elementu webhook protokołu HTTP jest również oparta na zdarzeniach i *subskrybuje* określoną usługę lub punkt końcowy przez zarejestrowanie *adresu URL wywołania zwrotnego* za pomocą tej usługi lub punktu końcowego. Akcja elementu webhook wstrzymuje przepływ pracy aplikacji logiki i czeka, aż usługa lub punkt końcowy wywoła adres URL przed wznowieniem działania aplikacji logiki. Aplikacja logiki akcji *anuluje subskrypcję* usługi lub punktu końcowego w następujących przypadkach:

* Po pomyślnym zakończeniu akcji elementu webhook
* Jeśli uruchomienie aplikacji logiki zostało anulowane podczas oczekiwania na odpowiedź
* Przed upływem limitu czasu aplikacji logiki

Na przykład akcja [**Wyślij wiadomość e-mail**](connectors-create-api-office365-outlook.md) dotyczącą zatwierdzenia przez łącznik programu Outlook pakietu Office 365 to przykład akcji elementu webhook, która następuje po tym wzorcu. Ten wzorzec można przyciągnąć do dowolnej usługi za pomocą akcji elementu webhook.

> [!NOTE]
> Logic Apps wymusza Transport Layer Security (TLS) 1,2 podczas otrzymywania wywołania z powrotem do wyzwalacza lub akcji elementu webhook protokołu HTTP. Jeśli widzisz błędy uzgadniania protokołu TLS, upewnij się, że korzystasz z protokołu TLS 1,2. W przypadku wywołań przychodzących Oto obsługiwane mechanizmy szyfrowania:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Więcej informacji można znaleźć w następujących tematach:

* [Parametry wyzwalacza elementu webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Elementy webhook i subskrypcje](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Tworzenie niestandardowych interfejsów API, które obsługują element webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL już wdrożonego punktu końcowego lub interfejsu API, który obsługuje wzorzec elementu webhook i subskrypcja anulowania subskrypcji dla [wyzwalaczy elementu webhook w usłudze Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) lub [akcji elementu webhook w usłudze Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) zgodnie z potrzebami

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, w której chcesz czekać na określone zdarzenia w docelowym punkcie końcowym. Aby rozpocząć pracę z wyzwalaczem elementu webhook protokołu HTTP, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji elementu webhook protokołu HTTP, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. Ten przykład używa wyzwalacza HTTP jako pierwszego kroku.

## <a name="add-an-http-webhook-trigger"></a>Dodawanie wyzwalacza elementu webhook protokołu HTTP

Ten wbudowany wyzwalacz wywołuje punkt końcowy subskrypcji w usłudze docelowej i rejestruje adres URL wywołania zwrotnego przy użyciu usługi docelowej. Aplikacja logiki czeka, aż usługa docelowa wyśle `HTTP POST` żądanie do adresu URL wywołania zwrotnego. Po wystąpieniu tego zdarzenia wyzwalacz uruchamia i przekazuje wszystkie dane w żądaniu wraz z przepływem pracy.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania projektanta wprowadź `http webhook` jako filtr. Z listy **wyzwalacze** Wybierz wyzwalacz **http elementu webhook** .

   ![Wybierz wyzwalacz elementu webhook protokołu HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Ten przykład zmienia nazwę wyzwalacza na `HTTP Webhook trigger` tak, aby krok miał bardziej opisową nazwę. Ponadto przykład później dodaje akcję elementu webhook protokołu HTTP i obie nazwy muszą być unikatowe.

1. Podaj wartości [parametrów wyzwalacza elementu webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) , które mają być używane dla wywołań subskrybowania i anulowania subskrypcji.

   W tym przykładzie wyzwalacz zawiera metody, identyfikatory URI i treści wiadomości, które mają być używane podczas wykonywania operacji subskrybowania i anulowania subskrypcji.

   ![Wprowadź parametry wyzwalacza elementu webhook protokołu HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Subskrypcja — Metoda** | Tak | Metoda do użycia podczas subskrybowania docelowego punktu końcowego |
   | **Subskrypcja — identyfikator URI** | Tak | Adres URL, który ma być używany do subskrybowania docelowego punktu końcowego |
   | **Subskrypcja — treść** | Nie | Każda treść komunikatu do uwzględnienia w żądaniu subskrybowania. Ten przykład zawiera adres URL wywołania zwrotnego, który jednoznacznie identyfikuje subskrybenta, który jest aplikacją logiki, przy użyciu `@listCallbackUrl()` wyrażenia w celu pobrania adresu URL wywołania zwrotnego aplikacji logiki. |
   | **Unsubskrybuj — Metoda** | Nie | Metoda do użycia podczas anulowania subskrypcji z docelowego punktu końcowego |
   | **Anulowanie subskrypcji — identyfikator URI** | Nie | Adres URL, który będzie używany do anulowania subskrypcji z docelowego punktu końcowego |
   | **Anulowanie subskrypcji — treść** | Nie | Opcjonalna treść komunikatu do uwzględnienia w żądaniu anulowania subskrypcji <p><p>**Uwaga**: Ta właściwość nie obsługuje korzystania z `listCallbackUrl()` funkcji. Jednak wyzwalacz automatycznie uwzględnia i wysyła nagłówki, `x-ms-client-tracking-id` a także `x-ms-workflow-operation-name` , których usługa docelowa może użyć do unikatowego identyfikowania abonenta. |
   ||||

1. Aby dodać inne właściwości wyzwalacza, Otwórz listę **Dodaj nowy parametr** .

   ![Dodaj więcej właściwości wyzwalacza](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Na przykład, jeśli konieczne jest użycie uwierzytelniania, można dodać właściwości **subskrybowania** i uwierzytelniania i **anulowania subskrypcji** . Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla elementu webhook protokołu HTTP, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki przy użyciu akcji uruchamianych podczas uruchamiania wyzwalacza.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Zapisanie aplikacji logiki wywołuje punkt końcowy subskrypcji w usłudze docelowej i rejestruje adres URL wywołania zwrotnego. Aplikacja logiki czeka, aż usługa docelowa wyśle `HTTP POST` żądanie do adresu URL wywołania zwrotnego. Po wystąpieniu tego zdarzenia wyzwalacz uruchamia i przekazuje wszystkie dane w żądaniu wraz z przepływem pracy. Jeśli ta operacja zakończy się pomyślnie, wyzwalacz anuluje subskrypcję z punktu końcowego, a aplikacja logiki kontynuuje pozostałe przepływy pracy.

## <a name="add-an-http-webhook-action"></a>Dodaj akcję elementu webhook protokołu HTTP

Ta wbudowana akcja wywołuje punkt końcowy subskrypcji w usłudze docelowej i rejestruje adres URL wywołania zwrotnego przy użyciu usługi docelowej. Następnie aplikacja logiki wstrzymuje i czeka, aż usługa docelowa wyśle `HTTP POST` żądanie do adresu URL wywołania zwrotnego. Gdy to zdarzenie wystąpi, Akcja przekazuje wszystkie dane w żądaniu wraz z przepływem pracy. Jeśli operacja zakończy się pomyślnie, Akcja anuluje subskrypcję z punktu końcowego, a aplikacja logiki kontynuuje działanie pozostałego przepływu pracy.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

   Ten przykład używa wyzwalacza elementu webhook protokołu HTTP jako pierwszego kroku.

1. W kroku, w którym chcesz dodać akcję elementu webhook protokołu HTTP, wybierz pozycję **nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania projektanta wprowadź `http webhook` jako filtr. Z listy **Akcje** wybierz akcję **element webhook protokołu HTTP** .

   ![Wybierz akcję elementu webhook protokołu HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Ten przykład zmienia nazwę akcji na "akcja elementu webhook protokołu HTTP" tak, aby krok miał bardziej opisową nazwę.

1. Podaj wartości parametrów akcji elementu webhook protokołu HTTP, które są podobne do [parametrów wyzwalacza elementu webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), które mają być używane dla wywołań subskrybowania i anulowania subskrypcji.

   W tym przykładzie akcja obejmuje metody, identyfikatory URI i treści komunikatów, które mają być używane podczas wykonywania operacji subskrybowania i anulowania subskrypcji.

   ![Wprowadź parametry akcji elementu webhook protokołu HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Subskrypcja — Metoda** | Tak | Metoda do użycia podczas subskrybowania docelowego punktu końcowego |
   | **Subskrypcja — identyfikator URI** | Tak | Adres URL, który ma być używany do subskrybowania docelowego punktu końcowego |
   | **Subskrypcja — treść** | Nie | Każda treść komunikatu do uwzględnienia w żądaniu subskrybowania. Ten przykład zawiera adres URL wywołania zwrotnego, który jednoznacznie identyfikuje subskrybenta, który jest aplikacją logiki, przy użyciu `@listCallbackUrl()` wyrażenia w celu pobrania adresu URL wywołania zwrotnego aplikacji logiki. |
   | **Unsubskrybuj — Metoda** | Nie | Metoda do użycia podczas anulowania subskrypcji z docelowego punktu końcowego |
   | **Anulowanie subskrypcji — identyfikator URI** | Nie | Adres URL, który będzie używany do anulowania subskrypcji z docelowego punktu końcowego |
   | **Anulowanie subskrypcji — treść** | Nie | Opcjonalna treść komunikatu do uwzględnienia w żądaniu anulowania subskrypcji <p><p>**Uwaga**: Ta właściwość nie obsługuje korzystania z `listCallbackUrl()` funkcji. Jednak akcja automatycznie uwzględnia i wysyła nagłówki oraz `x-ms-client-tracking-id` `x-ms-workflow-operation-name` , których usługa docelowa może użyć do unikatowego identyfikowania abonenta. |
   ||||

1. Aby dodać inne właściwości akcji, Otwórz listę **Dodaj nowy parametr** .

   ![Dodaj więcej właściwości akcji](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Na przykład, jeśli konieczne jest użycie uwierzytelniania, można dodać właściwości **subskrybowania** i uwierzytelniania i **anulowania subskrypcji** . Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla elementu webhook protokołu HTTP, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Po zakończeniu Pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Teraz po uruchomieniu tej akcji aplikacja logiki wywołuje punkt końcowy subskrypcji w usłudze docelowej i rejestruje adres URL wywołania zwrotnego. Następnie aplikacja logiki wstrzymuje przepływ pracy i czeka na wysłanie `HTTP POST` żądania do adresu URL wywołania zwrotnego przez usługę docelową. Gdy to zdarzenie wystąpi, Akcja przekazuje wszystkie dane w żądaniu wraz z przepływem pracy. Jeśli operacja zakończy się pomyślnie, Akcja anuluje subskrypcję z punktu końcowego, a aplikacja logiki kontynuuje działanie pozostałego przepływu pracy.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji na temat parametrów wyzwalacza i akcji, które są podobne do siebie, zobacz [Parametry elementu webhook protokołu HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Szczegóły danych wyjściowych

Poniżej znajduje się więcej informacji na temat danych wyjściowych wyzwalacza lub akcji elementu webhook protokołu HTTP, które zwracają następujące informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| nagłówka | object | Nagłówki żądania |
| body | object | Obiekt JSON | Obiekt z zawartością treści z żądania |
| kod stanu | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowano |
| 400 | Złe żądanie |
| 401 | Brak autoryzacji |
| 403 | Forbidden |
| 404 | Nie znaleziono |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
