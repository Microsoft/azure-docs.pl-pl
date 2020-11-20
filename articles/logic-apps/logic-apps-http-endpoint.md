---
title: Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki przy użyciu wyzwalaczy żądań
description: Konfigurowanie punktów końcowych HTTPS do wywoływania, wyzwalania lub zagnieżdżania przepływów pracy aplikacji logiki w Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: b345168dad63b1846d46c12721587eaffb5f887e
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981208"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki za pomocą punktów końcowych HTTPS w Azure Logic Apps

Aby umożliwić aplikacji logiki wywoływanie przy użyciu adresu URL i odbierać żądania przychodzące z innych usług, można natywnie uwidocznić synchroniczny punkt końcowy HTTPS przy użyciu wyzwalacza opartego na żądaniach w aplikacji logiki. Dzięki tej funkcji można wywołać aplikację logiki z innych aplikacji logiki i utworzyć wzorzec wywoływanych punktów końcowych. Aby skonfigurować możliwy do wywołania punkt końcowy do obsługi wywołań przychodzących, można użyć dowolnego z tych typów wyzwalaczy:

* [Żądanie](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Wyzwalacze łączników zarządzanych, które mają [Typ ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i mogą odbierać przychodzące żądania https

W tym artykule pokazano, jak utworzyć możliwy do wywołania punkt końcowy w aplikacji logiki przy użyciu wyzwalacza żądania i wywołać ten punkt końcowy z innej aplikacji logiki. Wszystkie zasady są stosowane identycznie z innymi typami wyzwalaczy, których można użyć do odbierania żądań przychodzących.


Aby uzyskać więcej informacji o zabezpieczeniach, autoryzacji i szyfrowaniu wywołań przychodzących do aplikacji logiki, takiej jak [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), wcześniej znanej jako SSL (SSL), [Azure Active Directory otwarte uwierzytelnianie (Azure AD OAuth)](../active-directory/develop/index.yml), Uwidacznianie aplikacji logiki za pomocą usługi Azure API Management lub ograniczanie adresów IP, które pochodzą z wywołań przychodzących, zobacz [bezpieczny dostęp i dostęp do danych dla wywołań przychodzących do wyzwalaczy opartych na żądaniach](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której ma zostać użyty wyzwalacz do utworzenia możliwego do przetworzenia punktu końcowego. Możesz zacząć od pustej aplikacji logiki lub istniejącej aplikacji logiki, w której można zastąpić bieżący wyzwalacz. Ten przykład rozpoczyna się od pustej aplikacji logiki. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zobacz [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-callable-endpoint"></a>Utwórz możliwy do nawoływać punkt końcowy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Utwórz i Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `request` jako filtr. Z listy Wyzwalacze wybierz opcję **po odebraniu żądania HTTP**.

   ![Znajdź i wybierz wyzwalacz żądania](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Opcjonalnie w polu **schemat JSON treści żądania** można wprowadzić schemat JSON, który opisuje ładunek lub dane, które oczekują na otrzymanie wyzwalacza.

   Projektant używa tego schematu do generowania tokenów reprezentujących wyjściowe wyzwalacze. Następnie możesz łatwo odwoływać się do tych danych wyjściowych w przepływie pracy aplikacji logiki. Dowiedz się więcej o [tokenach wygenerowanych na podstawie schematów JSON](#generated-tokens).

   W tym przykładzie wprowadź następujący schemat:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Podaj schemat JSON dla akcji żądania](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Lub można wygenerować schemat JSON, dostarczając przykładowy ładunek:

   1. W wyzwalaczu **żądania** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

   1. W polu **wprowadź lub wklej przykładowy ładunek JSON** wprowadź przykładowy ładunek, na przykład:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Gdy wszystko będzie gotowe, wybierz pozycję **gotowe**.

      Pole **schematu JSON treści żądania** zawiera teraz wygenerowany schemat.

1. Zapisz aplikację logiki.

   W polu **adres URL post protokołu HTTP** jest teraz wyświetlany wygenerowany adres URL wywołania zwrotnego, który może być używany przez inne usługi do wywoływania i wyzwalania aplikacji logiki. Ten adres URL zawiera parametry zapytania, które określają klucz sygnatury dostępu współdzielonego, który jest używany do uwierzytelniania.

   ![Wygenerowany adres URL wywołania zwrotnego dla punktu końcowego](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. Aby skopiować adres URL wywołania zwrotnego, dostępne są następujące opcje:

   * W polu **adres URL post protokołu HTTP** wybierz pozycję **Kopiuj adres URL** (ikona Kopiuj pliki).

   * Wykonaj to wywołanie przy użyciu metody, której oczekuje wyzwalacz żądania. Ten przykład używa `POST` metody:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Skopiuj adres URL wywołania zwrotnego z okienka **Przegląd** aplikacji logiki.

     1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

     1. W sekcji **Podsumowanie** wybierz pozycję **Zobacz historię wyzwalacza**.

        ![Pobierz adres URL punktu końcowego z Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. W polu **adres URL wywołania zwrotnego [Post]** Skopiuj adres URL:

        ![Kopiuj adres URL punktu końcowego z Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Wybierz oczekiwaną metodę żądania

Domyślnie wyzwalacz żądania oczekuje na `POST` żądanie. Można jednak określić inną metodę, której obiekt wywołujący musi używać, ale tylko jedną metodę.

1. W wyzwalaczu żądania Otwórz listę **Dodaj nowy parametr** , a następnie wybierz **metodę**, która dodaje tę właściwość do wyzwalacza.

   ![Dodaj właściwość "Method" do wyzwalacza](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Z listy **Metoda** wybierz metodę, której wyzwalacz powinien oczekiwać. Lub można określić metodę niestandardową.

   Na przykład wybierz metodę **Get** , aby można było przetestować adres URL punktu końcowego później.

   ![Wybierz metodę żądania oczekiwaną przez wyzwalacz](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Przekaż parametry za poorednictwem adresu URL punktu końcowego

Aby akceptować wartości parametrów za pomocą adresu URL punktu końcowego, dostępne są następujące opcje:

* [Akceptuj wartości za poorednictwem parametrów pobierania](#get-parameters) lub parametrów adresu URL.

  Te wartości są przesyłane jako pary nazwa-wartość w adresie URL punktu końcowego. W przypadku tej opcji należy użyć metody GET w wyzwalaczu żądania. W kolejnej akcji można pobrać wartości parametrów jako dane wyjściowe wyzwalacza przy użyciu `triggerOutputs()` funkcji w wyrażeniu.

* [Akceptuj wartości za pomocą ścieżki względnej](#relative-path) dla parametrów w wyzwalaczu żądania.

  Te wartości są przesyłane za pomocą ścieżki względnej w adresie URL punktu końcowego. Należy również jawnie [wybrać metodę](#select-method) oczekiwaną przez wyzwalacz. W kolejnej akcji można pobrać wartości parametrów jako dane wyjściowe wyzwalacza, odwołując się bezpośrednio do tych danych wyjściowych.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Akceptuj wartości za poorednictwem parametrów GET

1. W wyzwalaczu żądania Otwórz **listę Dodaj nowy parametr**, Dodaj właściwość **metody** do wyzwalacza i wybierz metodę **Get** .

   Aby uzyskać więcej informacji, zobacz [Wybierz oczekiwaną metodę żądania](#select-method).

1. W obszarze wyzwalacza żądania Dodaj akcję, w której chcesz użyć wartości parametru. Na potrzeby tego przykładu Dodaj akcję **odpowiedź** .

   1. W obszarze wyzwalacz żądania wybierz pozycję **nowy krok**  >  **Dodaj akcję**.
   
   1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `response` jako filtr. Z listy Akcje wybierz akcję **odpowiedź** .

1. Aby skompilować `triggerOutputs()` wyrażenie pobierające wartość parametru, wykonaj następujące kroki:

   1. Kliknij wewnątrz właściwości **treści** akcji odpowiedzi, aby wyświetlić listę zawartości dynamicznej, a następnie wybierz pozycję **wyrażenie**.

   1. W polu **wyrażenie** wprowadź następujące wyrażenie, zastępując `parameter-name` je nazwą parametru, a następnie wybierz **przycisk OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Dodaj wyrażenie "triggerOutputs ()" do wyzwolenia](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      We właściwości **Body** wyrażenie jest rozpoznawane jako `triggerOutputs()` token.

      ![Rozwiązano wyrażenie "triggerOutputs ()"](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Jeśli zapiszesz aplikację logiki, opuścisz projektanta i wrócisz do projektanta, w polu token zostanie wyświetlona określona nazwa parametru, na przykład:

      ![Rozpoznane wyrażenie dla nazwy parametru](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      W widoku kodu Właściwość **Body** jest wyświetlana w definicji akcji odpowiedzi w następujący sposób:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Załóżmy na przykład, że chcesz przekazać wartość dla parametru o nazwie `postalCode` . Właściwość **Body** określa ciąg, `Postal Code: ` z końcowym miejscem, po którym następuje odpowiednie wyrażenie:

      ![Dodaj przykładowe wyrażenie "triggerOutputs ()", które ma zostać wyzwolone](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Aby przetestować możliwy do przetestowania punkt końcowy, skopiuj adres URL wywołania zwrotnego z wyzwalacza żądania, a następnie wklej adres URL do innego okna przeglądarki. W adresie URL Dodaj nazwę parametru i wartość po znaku zapytania ( `?` ) do adresu URL w następującym formacie, a następnie naciśnij klawisz ENTER.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   Przeglądarka zwróci odpowiedź z tym tekstem: `Postal Code: 123456`

   ![Odpowiedź od wysłania żądania do adresu URL wywołania zwrotnego](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Aby umieścić nazwę i wartość parametru w innej pozycji w adresie URL, upewnij się, że używasz znaku handlowego "i" ( `&` ) jako prefiksu, na przykład:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Ten przykład pokazuje adres URL wywołania zwrotnego z przykładową nazwą parametru i wartością `postalCode=123456` w różnych pozycjach w adresie URL:

   * Pozycja 1: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * drugie położenie: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Jeśli chcesz uwzględnić skrót lub symbol funta ( **#** ) w identyfikatorze URI, zamiast tego użyj tej kodowanej wersji: `%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Akceptowanie wartości za pomocą ścieżki względnej

1. W wyzwalaczu żądania Otwórz listę **Dodaj nowy parametr** i wybierz pozycję **ścieżka względna**, która dodaje tę właściwość do wyzwalacza.

   ![Dodaj właściwość "ścieżka względna" do wyzwalacza](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. We właściwości **ścieżka względna** określ ścieżkę względną dla parametru w SCHEMAcie JSON, który ma być akceptowany przez adres URL, na przykład `/address/{postalCode}` .

   ![Określ ścieżkę względną dla parametru](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. W obszarze wyzwalacza żądania Dodaj akcję, w której chcesz użyć wartości parametru. Na potrzeby tego przykładu Dodaj akcję **odpowiedź** .

   1. W obszarze wyzwalacz żądania wybierz pozycję **nowy krok**  >  **Dodaj akcję**.

   1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `response` jako filtr. Z listy Akcje wybierz akcję **odpowiedź** .

1. We właściwości **treści** akcji odpowiedzi Dołącz token, który reprezentuje parametr określony w ścieżce względnej wyzwalacza.

   Załóżmy na przykład, że akcja odpowiedzi powinna zostać zwrócona `Postal Code: {postalCode}` .

   1. We właściwości **Body** wprowadź `Postal Code: ` spację końcową. Pozostaw kursor wewnątrz pola edycji, aby lista zawartości dynamicznej pozostała otwarta.

   1. Z listy zawartość dynamiczna z sekcji **po odebraniu żądania HTTP** wybierz token **KodPocztowy** .

      ![Dodaj określony parametr do treści odpowiedzi](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      Właściwość **Body** zawiera teraz wybrany parametr:

      ![Przykładowa treść odpowiedzi z parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Zapisz aplikację logiki.

   W wyzwalaczu żądania adres URL wywołania zwrotnego jest aktualizowany i teraz zawiera ścieżkę względną, na przykład:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Aby przetestować możliwy do przetestowania punkt końcowy, skopiuj zaktualizowany adres URL wywołania zwrotnego z wyzwalacza żądania, wklej adres URL do innego okna przeglądarki, Zastąp ciąg `{postalCode}` w adresie URL `123456` , a następnie naciśnij klawisz ENTER.

   Przeglądarka zwróci odpowiedź z tym tekstem: `Postal Code: 123456`

   ![Odpowiedź od wysłania żądania do adresu URL wywołania zwrotnego](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Jeśli chcesz uwzględnić skrót lub symbol funta ( **#** ) w identyfikatorze URI, zamiast tego użyj tej kodowanej wersji: `%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>Wywoływanie aplikacji logiki przy użyciu adresu URL punktu końcowego

Po utworzeniu punktu końcowego można wyzwolić aplikację logiki, wysyłając żądanie HTTPS do pełnego adresu URL punktu końcowego. Aplikacje logiki mają wbudowaną obsługę punktów końcowych dostępu bezpośredniego.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokeny wygenerowane na podstawie schematu

Po podaniu schematu JSON w wyzwalaczu żądania projektant aplikacji logiki generuje tokeny dla właściwości w tym schemacie. Następnie można użyć tych tokenów do przekazywania danych za pomocą przepływu pracy aplikacji logiki.

Jeśli na przykład dodasz więcej właściwości, takich jak `"suite"` , do schematu JSON, tokeny dla tych właściwości są dostępne do użycia w kolejnych krokach dla aplikacji logiki. Oto kompletny schemat JSON:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Tworzenie zagnieżdżonych aplikacji logiki

Przepływy pracy można zagnieżdżać w aplikacji logiki, dodając inne aplikacje logiki, które mogą odbierać żądania. Aby dołączyć te aplikacje logiki, wykonaj następujące kroki:

1. W kroku, w którym chcesz wywołać inną aplikację logiki, wybierz pozycję **nowy krok**  >  **Dodaj akcję**.

1. W obszarze **Wybierz akcję** wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `logic apps` jako filtr. Z listy Akcje wybierz pozycję **Wybierz przepływ pracy Logic Apps**.

   ![Zagnieżdżanie aplikacji logiki w bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Projektant pokazuje kwalifikujące się Aplikacje logiki do wybrania.

1. Wybierz aplikację logiki, która ma zostać wywołana z bieżącej aplikacji logiki.

   ![Wybierz aplikację logiki do wywołania z bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Odwołuje się do zawartości z przychodzącego żądania

Jeśli typ zawartości żądania przychodzącego to `application/json` , można odwołać się do właściwości w żądaniu przychodzącym. W przeciwnym razie ta zawartość jest traktowana jako pojedyncza jednostka binarna, którą można przekazać do innych interfejsów API. Aby odwołać się do tej zawartości w przepływie pracy aplikacji logiki, należy najpierw skonwertować tę zawartość.

Na przykład jeśli przekazujesz zawartość, która ma `application/xml` Typ, możesz użyć [ `@xpath()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#xpath) do przeprowadzenia wyodrębniania XPath lub użyć [ `@json()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#json) do konwertowania XML na notację JSON. Dowiedz się więcej o pracy z obsługiwanymi [typami zawartości](../logic-apps/logic-apps-content-type.md).

Aby uzyskać dane wyjściowe z przychodzącego żądania, można użyć [ `@triggerOutputs` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Załóżmy na przykład, że dane wyjściowe wyglądają podobnie do tego przykładu:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Aby uzyskać dostęp do `body` właściwości, można użyć [ `@triggerBody()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) jako skrótu.

## <a name="respond-to-requests"></a>Odpowiadanie na żądania

Czasami chcesz odpowiedzieć na określone żądania wyzwalające aplikację logiki, zwracając zawartość do obiektu wywołującego. Aby skonstruować kod stanu, nagłówek i treść odpowiedzi, użyj akcji odpowiedzi. Ta akcja może pojawić się w dowolnym miejscu w aplikacji logiki, a nie tylko na końcu przepływu pracy. Jeśli aplikacja logiki nie zawiera akcji odpowiedzi, punkt końcowy odpowie *natychmiast* po **zaakceptowanym stanie 202** .

Aby oryginalny obiekt wywołujący pomyślnie przetworzył odpowiedź, wszystkie wymagane kroki dla odpowiedzi muszą zakończyć się w limicie [limitu czasu żądania](./logic-apps-limits-and-config.md) , chyba że wyzwolona aplikacja logiki jest wywoływana jako zagnieżdżona aplikacja logiki. Jeśli w tym limicie nie zostanie zwrócona odpowiedź, żądanie przychodzące **przekroczy limit czasu** i otrzyma odpowiedź 408.

W przypadku zagnieżdżonych aplikacji logiki nadrzędna aplikacja logiki kontynuuje oczekiwanie na odpowiedź do momentu zakończenia wszystkich kroków, niezależnie od tego, ile czasu jest wymagane.

### <a name="construct-the-response"></a>Konstruowanie odpowiedzi

W treści odpowiedzi można uwzględnić wiele nagłówków i zawartość dowolnego typu. Na przykład nagłówek tej odpowiedzi określa, że typ zawartości odpowiedzi to `application/json` i że treść zawiera wartości `town` `postalCode` właściwości i, w oparciu o schemat JSON opisany wcześniej w tym temacie dla wyzwalacza żądania.

![Podaj zawartość odpowiedzi dla akcji odpowiedzi HTTPS](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpowiedzi mają następujące właściwości:

| Właściwość (Display) | Właściwość (JSON) | Opis |
|--------------------|-----------------|-------------|
| **Kod stanu** | `statusCode` | Kod stanu protokołu HTTPS, który ma być używany w odpowiedzi dla żądania przychodzącego. Ten kod może być dowolnym prawidłowym kodem stanu zaczynającym się od 2xx, 4xx lub 5xx. Jednak kody stanu 3xx są niedozwolone. |
| **Nagłówki** | `headers` | Co najmniej jeden nagłówek do uwzględnienia w odpowiedzi |
| **Treść** | `body` | Obiekt treści, który może być ciągiem, obiektem JSON lub parzystą zawartością binarną, do której odwołuje się poprzedni krok |
||||

Aby wyświetlić definicję JSON dla akcji odpowiedzi i kompletnej definicji JSON aplikacji logiki, na pasku narzędzi projektanta aplikacji logiki wybierz pozycję **Widok kodu**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Pytania i odpowiedzi

#### <a name="q-what-about-url-security"></a>P: Jakie są zabezpieczenia adresów URL?

Odp **.: usługa** Azure bezpiecznie generuje adresy URL wywołania zwrotnego aplikacji logiki przy użyciu [sygnatury dostępu współdzielonego (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature). Ten podpis przechodzi przez parametr zapytania i musi być zweryfikowany, aby można było uruchomić aplikację logiki. Platforma Azure generuje podpis przy użyciu unikatowej kombinacji klucza tajnego na aplikację logiki, nazwę wyzwalacza i wykonywaną operację. Jeśli jednak ktoś nie ma dostępu do klucza aplikacji logiki tajnej, nie może wygenerować prawidłowego podpisu.

> [!IMPORTANT]
> W przypadku systemów bezpieczeństwa i wyższych zabezpieczeń zdecydowanie odradzamy wywoływanie aplikacji logiki bezpośrednio z przeglądarki z następujących powodów:
>
> * Klucz dostępu współdzielonego zostanie wyświetlony w adresie URL.
> * Nie można zarządzać zasadami zawartości zabezpieczeń ze względu na domeny udostępnione w ramach klientów Azure Logic Apps.

Aby uzyskać więcej informacji o zabezpieczeniach, autoryzacji i szyfrowaniu wywołań przychodzących do aplikacji logiki, takiej jak [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), wcześniej znanej jako SSL (SSL), [Azure Active Directory otwarte uwierzytelnianie (Azure AD OAuth)](../active-directory/develop/index.yml), Uwidacznianie aplikacji logiki za pomocą usługi Azure API Management lub ograniczanie adresów IP, które pochodzą z wywołań przychodzących, zobacz [bezpieczny dostęp i dostęp do danych dla wywołań przychodzących do wyzwalaczy opartych na żądaniach](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)

#### <a name="q-can-i-configure-callable-endpoints-further"></a>P: Czy można kontynuować konfigurowanie wywoływanych punktów końcowych?

Odp **.: tak**, punkty końcowe HTTPS obsługują bardziej zaawansowaną konfigurację za pośrednictwem [usługi Azure API Management](../api-management/api-management-key-concepts.md). Ta usługa oferuje również możliwość spójnego zarządzania wszystkimi interfejsami API, w tym aplikacjami logiki, Konfigurowanie niestandardowych nazw domen, korzystanie z większej liczby metod uwierzytelniania i inne, na przykład:

* [Zmień metodę żądania](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Zmiana segmentów adresu URL żądania](../api-management/api-management-transformation-policies.md#RewriteURL)
* Skonfiguruj domeny API Management w [Azure Portal](https://portal.azure.com/)
* Konfigurowanie zasad do sprawdzania uwierzytelniania podstawowego

## <a name="next-steps"></a>Następne kroki

* [Odbieraj przychodzące wywołania HTTPS i odpowiadaj na nie przy użyciu Azure Logic Apps](../connectors/connectors-native-reqres.md)
* [Bezpieczny dostęp i dane w Azure Logic Apps dostęp do wywołań przychodzących do wyzwalaczy opartych na żądaniach](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
