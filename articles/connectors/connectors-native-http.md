---
title: Wywoływanie punktów końcowych usługi przy użyciu protokołu HTTP lub HTTPS
description: Wysyłaj żądania wychodzące HTTP lub HTTPS do punktów końcowych usługi z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/09/2020
tags: connectors
ms.openlocfilehash: 23c6a555909d43f640fb5089fb60da8bac065886
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609531"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Wywoływanie punktów końcowych usługi za pośrednictwem protokołu HTTP lub HTTPS z Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego wyzwalacza http lub akcji można tworzyć automatyczne zadania i przepływy pracy, które wysyłają żądania do punktów końcowych usługi za pośrednictwem protokołu HTTP lub https. Na przykład można monitorować punkt końcowy usługi dla witryny sieci Web, sprawdzając ten punkt końcowy zgodnie z określonym harmonogramem. Po wystąpieniu określonego zdarzenia w tym punkcie końcowym, takim jak witryna sieci Web, zdarzenie wyzwala przepływ pracy aplikacji logiki i uruchamia akcje w tym przepływie pracy. Jeśli zamiast tego chcesz otrzymywać przychodzące wywołania HTTPS i odpowiadać na nie, użyj wbudowanego [wyzwalacza żądań lub akcji odpowiedzi](../connectors/connectors-native-reqres.md).

* Aby sprawdzić lub *sondować* punkt końcowy zgodnie z cyklicznym harmonogramem, [Dodaj wyzwalacz http](#http-trigger) jako pierwszy krok w przepływie pracy. Za każdym razem, gdy wyzwalacz sprawdza punkt końcowy, wyzwalacz wywołuje lub wysyła *żądanie* do punktu końcowego. Odpowiedź punktu końcowego określa, czy przepływ pracy aplikacji logiki zostanie uruchomiony. Wyzwalacz przekazuje zawartość z odpowiedzi punktu końcowego do akcji w aplikacji logiki.

* Aby wywołać punkt końcowy z dowolnego miejsca w przepływie pracy, [Dodaj akcję http](#http-action). Odpowiedź punktu końcowego określa, jak działają pozostałe akcje przepływu pracy.

W tym artykule opisano sposób dodawania wyzwalacza HTTP lub akcji do przepływu pracy aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL docelowego punktu końcowego, który ma zostać wywołany.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Aplikacja logiki, z której ma zostać wywołany docelowy punkt końcowy. Aby rozpocząć pracę z wyzwalaczem HTTP, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. Ten przykład używa wyzwalacza HTTP jako pierwszego kroku.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Dodawanie wyzwalacza HTTP

Ten wbudowany wyzwalacz wykonuje wywołanie HTTP do określonego adresu URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania projektanta wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `http` jako filtr. Z listy **wyzwalacze** Wybierz wyzwalacz **http** .

   ![Wybieranie wyzwalacza HTTP](./media/connectors-native-http/select-http-trigger.png)

   Ten przykład zmienia nazwę wyzwalacza na "wyzwalacz HTTP", tak aby krok miał bardziej opisową nazwę. Ponadto przykład później dodaje akcję HTTP, a obie nazwy muszą być unikatowe.

1. Podaj wartości [parametrów wyzwalacza http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) , które mają zostać uwzględnione w wywołaniu docelowego punktu końcowego. Skonfiguruj Cykl dla tego, jak często wyzwalacz ma sprawdzać docelowy punkt końcowy.

   ![Wprowadź parametry wyzwalacza HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   W przypadku wybrania typu uwierzytelniania innego niż **none**ustawienia uwierzytelniania różnią się w zależności od wybranej opcji. Aby uzyskać więcej informacji o typach uwierzytelniania dostępnych dla protokołu HTTP, zobacz następujące tematy:

   * [Dodawanie uwierzytelniania do wywołań wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Uwierzytelnianie dostępu do zasobów za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)

1. Aby dodać inne dostępne parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki przy użyciu akcji uruchamianych podczas uruchamiania wyzwalacza.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Dodaj akcję HTTP

Ta wbudowana akcja powoduje wywołanie HTTP do określonego adresu URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się do [portalu Azure](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

   Ten przykład używa wyzwalacza HTTP jako pierwszego kroku.

1. W kroku, w którym chcesz dodać akcję HTTP, wybierz pozycję **nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `http` jako filtr. Z listy **Akcje** wybierz akcję **http** .

   ![Wybieranie akcji HTTP](./media/connectors-native-http/select-http-action.png)

   Ten przykład zmienia nazwę akcji na "Akcja HTTP", aby krok miał bardziej opisową nazwę.

1. Podaj wartości [parametrów akcji http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) , które mają zostać uwzględnione w wywołaniu docelowego punktu końcowego.

   ![Wprowadź parametry akcji HTTP](./media/connectors-native-http/http-action-parameters.png)

   W przypadku wybrania typu uwierzytelniania innego niż **none**ustawienia uwierzytelniania różnią się w zależności od wybranej opcji. Aby uzyskać więcej informacji o typach uwierzytelniania dostępnych dla protokołu HTTP, zobacz następujące tematy:

   * [Dodawanie uwierzytelniania do wywołań wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Uwierzytelnianie dostępu do zasobów za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)

1. Aby dodać inne dostępne parametry, Otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Na podstawie możliwości docelowego punktu końcowego, wywołania wychodzące obsługują Transport Layer Security (TLS), które były wcześniej SSL (SSL), wersje 1,0, 1,1 i 1,2. Logic Apps negocjuje z punktem końcowym przy użyciu najwyższej obsługiwanej wersji.

Na przykład, jeśli punkt końcowy obsługuje 1,2, łącznik HTTP najpierw używa 1,2. W przeciwnym razie łącznik używa następnej najwyższej obsługiwanej wersji.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

* W przypadku aplikacji logiki w globalnym, wielodostępnym środowisku platformy Azure łącznik protokołu HTTP nie zezwala na certyfikaty TLS/SSL z podpisem własnym. Jeśli aplikacja logiki wysyła wywołanie HTTP do serwera i przedstawia certyfikat z podpisem własnym protokołu TLS/SSL, wywołanie HTTP kończy się niepowodzeniem z `TrustFailure` powodu błędu.

* W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)łącznik protokołu HTTP zezwala na certyfikaty z podpisem własnym dla UZGADNIANIA protokołów TLS/SSL. Należy jednak najpierw [włączyć obsługę certyfikatów](../logic-apps/create-integration-service-environment-rest-api.md#request-body) z podpisem własnym dla istniejących ISE lub nowych ISE przy użyciu interfejsu API REST Logic Apps i zainstalować certyfikat publiczny w `TrustedRoot` lokalizacji.

## <a name="content-with-multipartform-data-type"></a>Zawartość z typem wieloczęściowym/formularzem danych

Aby obsłużyć zawartość, która ma `multipart/form-data` Typ w żądaniach HTTP, można dodać obiekt JSON, który zawiera `$content-type` `$multipart` atrybuty i do treści żądania HTTP przy użyciu tego formatu.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Załóżmy na przykład, że masz aplikację logiki, która wysyła żądanie HTTP POST dla pliku programu Excel do witryny sieci Web za pomocą interfejsu API tej witryny, który obsługuje ten `multipart/form-data` Typ. Oto, jak ta akcja może wyglądać następująco:

![Wieloczęściowe dane formularza](./media/connectors-native-http/http-action-multipart.png)

Poniżej znajduje się ten sam przykład pokazujący definicję JSON akcji HTTP w źródłowej definicji przepływu pracy:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Asynchroniczne zachowanie żądania — odpowiedź

Domyślnie wszystkie działania oparte na protokole HTTP w Azure Logic Apps są zgodne ze [wzorcem standardowej operacji asynchronicznej](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply). Ten wzorzec określa, że po wykonaniu akcji HTTP lub wysłaniu żądania do punktu końcowego, usługi, systemu lub interfejsu API odbiornik natychmiast zwróci odpowiedź ["202 zaakceptowane"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) . Ten kod potwierdza, że odbiornik zaakceptował żądanie, ale nie zakończył przetwarzania. Odpowiedź może zawierać `location` nagłówek, który określa adres URL i identyfikator odświeżania, którego wywołujący może użyć do sondowania lub sprawdzenia stanu żądania asynchronicznego, dopóki odbiornik nie przestanie działać i zwróci odpowiedź ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) lub inną odpowiedź inną niż 202. Jednak obiekt wywołujący nie musi czekać na zakończenie przetwarzania żądania i może nadal uruchamiać następną akcję. Aby uzyskać więcej informacji, zobacz [asynchroniczne integrację mikrousług wymusza autonomię mikrousług](https://docs.microsoft.com/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* W Projektancie aplikacji logiki akcja HTTP, ale nie wyzwalacz, ma **asynchroniczne ustawienie wzorca** , które jest domyślnie włączone. To ustawienie określa, że obiekt wywołujący nie czeka na zakończenie przetwarzania i może przejść do następnej akcji, ale kontynuuje sprawdzanie stanu do momentu zatrzymania przetwarzania. W przypadku wyłączenia tego ustawienia określa, że obiekt wywołujący czeka na zakończenie przetwarzania przed przejściem do następnej akcji.

  Aby znaleźć to ustawienie, wykonaj następujące kroki:

  1. Na pasku tytułu akcji HTTP wybierz przycisk wielokropka (**...**), który spowoduje otwarcie ustawień akcji.

  1. Znajdź ustawienie **wzorca asynchronicznego** .

     ![Ustawienie "wzorzec asynchroniczny"](./media/connectors-native-http/asynchronous-pattern-setting.png)

* Definicja podstawowego JavaScript Object Notation akcji HTTP (JSON) jest niejawnie zgodna ze wzorcem operacji asynchronicznej.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Wyłącz operacje asynchroniczne

Czasami może być konieczne asynchroniczne zachowanie akcji HTTP w określonych scenariuszach, na przykład w przypadku, gdy chcesz:

* [Unikaj limitów czasu HTTP dla długotrwałych zadań](#avoid-http-timeouts)
* [Wyłącz sprawdzanie nagłówków lokalizacji](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Wyłącz ustawienie **wzorca asynchronicznego**

1. W Projektancie aplikacji logiki na pasku tytułu akcji HTTP wybierz przycisk wielokropka (**...**), który spowoduje otwarcie ustawień akcji.

1. Znajdź ustawienie **wzorca asynchronicznego** , Włącz ustawienie **Wyłącz** , jeśli jest włączone, a następnie wybierz pozycję **gotowe**.

   ![Wyłącz ustawienie "wzorzec asynchroniczny"](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Wyłącz wzorzec asynchroniczny w definicji JSON akcji

W definicji JSON powiązanej z akcją HTTP [Dodaj `"DisableAsyncPattern"` opcję operacji](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) do definicji akcji, aby akcja była zgodna ze wzorcem operacji synchronicznej. Aby uzyskać więcej informacji, zobacz również [Uruchamianie akcji w wzorcu operacji synchronicznej](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Unikaj limitów czasu HTTP dla długotrwałych zadań

Żądania HTTP mają [limit czasu](../logic-apps/logic-apps-limits-and-config.md#http-limits). Jeśli masz długotrwałą akcję HTTP, która przekroczy limit czasu z powodu tego limitu, masz następujące opcje:

* [Wyłącz wzorzec operacji asynchronicznej akcji http](#disable-asynchronous-operations) , aby akcja nie była stale sondowana lub nie sprawdza stanu żądania. Zamiast tego akcja czeka na odpowiedź odbiornika o stanie i wynikach po zakończeniu przetwarzania żądania.

* Zastąp akcję HTTP [akcją elementu webhook](../connectors/connectors-native-webhook.md), która czeka na odpowiedź odbiornika przy użyciu stanu i wyników po zakończeniu przetwarzania żądania.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Wyłącz sprawdzanie nagłówków lokalizacji

Niektóre punkty końcowe, usługi, systemy lub interfejsy API zwracają odpowiedź "202 zaakceptowane", która nie ma `location` nagłówka. Aby uniknąć sytuacji, w której akcja HTTP stale sprawdza stan żądania, gdy `location` nagłówek nie istnieje, można korzystać z następujących opcji:

* [Wyłącz wzorzec operacji asynchronicznej akcji http](#disable-asynchronous-operations) , aby akcja nie była stale sondowana lub nie sprawdza stanu żądania. Zamiast tego akcja czeka na odpowiedź odbiornika o stanie i wynikach po zakończeniu przetwarzania żądania.

* Zastąp akcję HTTP [akcją elementu webhook](../connectors/connectors-native-webhook.md), która czeka na odpowiedź odbiornika przy użyciu stanu i wyników po zakończeniu przetwarzania żądania.

## <a name="known-issues"></a>Znane problemy

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Pominięte nagłówki HTTP

Jeśli wyzwalacz lub akcja HTTP zawiera te nagłówki, Logic Apps usuwa te nagłówki z wygenerowanego komunikatu żądania bez wyświetlania ostrzeżenia lub błędu:

* `Accept-*`
* `Allow`
* `Content-*`z następującymi wyjątkami: `Content-Disposition` , `Content-Encoding` , i`Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Mimo że Logic Apps nie zatrzyma zapisywania aplikacji logiki, które używają wyzwalacza HTTP lub akcji z tymi nagłówkami, Logic Apps ignoruje te nagłówki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji na temat wyzwalaczy i parametrów akcji, zobacz następujące sekcje:

* [Parametry wyzwalacza HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametry akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Szczegóły danych wyjściowych

Poniżej znajduje się więcej informacji na temat danych wyjściowych wyzwalacza HTTP lub akcji, która zwraca te informacje:

| Właściwość | Typ | Opis |
|----------|------|-------------|
| `headers` | Obiekt JSON | Nagłówki żądania |
| `body` | Obiekt JSON | Obiekt z zawartością treści z żądania |
| `status code` | Integer | Kod stanu z żądania |
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
