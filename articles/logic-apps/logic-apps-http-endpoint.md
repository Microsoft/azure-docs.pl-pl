---
title: Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki
description: Konfigurowanie punktów końcowych HTTP do wywoływania, wyzwalania lub zagnieżdżania przepływów pracy aplikacji logiki w Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77191332"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki za pomocą punktów końcowych HTTP w Azure Logic Apps

Aby umożliwić aplikacji logiki wywoływanie za pośrednictwem adresu URL, dzięki czemu aplikacja logiki może odbierać przychodzące żądania z innych usług, można natywnie uwidocznić synchroniczny punkt końcowy HTTP jako wyzwalacz w tej aplikacji logiki. Po skonfigurowaniu tej funkcji można także zagnieżdżać aplikację logiki wewnątrz innych aplikacji logiki, co umożliwia tworzenie wzorców wywoływanych punktów końcowych.

Aby skonfigurować punkt końcowy HTTP, można użyć dowolnego z tych typów wyzwalaczy, które umożliwiają aplikacjom logiki Odbieranie przychodzących żądań:

* [Request](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Wyzwalacze łączników zarządzanych, które mają [Typ ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i mogą odbierać przychodzące żądania http

> [!NOTE]
> W tych przykładach użyto wyzwalacza żądania, ale można użyć dowolnego wyzwalacza opartego na żądaniach HTTP, który znajduje się na poprzedniej liście. Wszystkie zasady identycznie stosują się do tych innych typów wyzwalacza.

Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zobacz [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której chcesz skonfigurować punkt końcowy HTTP jako wyzwalacz. Możesz zacząć od pustej aplikacji logiki lub istniejącej aplikacji logiki, w której chcesz zastąpić bieżący wyzwalacz. Ten przykład rozpoczyna się od pustej aplikacji logiki.

## <a name="create-a-callable-endpoint"></a>Utwórz możliwy do nawoływać punkt końcowy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Utwórz i Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

   W tym przykładzie użyto wyzwalacza żądania, ale można użyć dowolnego wyzwalacza, który może odbierać przychodzące żądania HTTP. Wszystkie zasady identycznie stosują te wyzwalacze. Aby uzyskać więcej informacji na temat wyzwalacza żądania, zobacz [Odbieranie i odpowiadanie na przychodzące wywołania HTTPS przy użyciu Azure Logic Apps](../connectors/connectors-native-reqres.md).

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

   **Wpis http post do tego adresu URL** zawiera teraz wygenerowany adres URL wywołania zwrotnego, który może być używany przez inne usługi do wywoływania i wyzwalania aplikacji logiki. Ten adres URL zawiera klucz sygnatury dostępu współdzielonego, który jest używany do uwierzytelniania w parametrach zapytania, na przykład:

   ![Wygenerowany adres URL wywołania zwrotnego dla punktu końcowego](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Możesz również uzyskać adres URL punktu końcowego HTTP z okienka **Przegląd** aplikacji logiki.

   1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

   1. W sekcji **Podsumowanie** wybierz pozycję **Zobacz historię wyzwalacza**.

      ![Pobierz adres URL punktu końcowego HTTP z Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. W polu **adres URL wywołania zwrotnego [Post]** Skopiuj adres URL:

      ![Kopiuj adres URL punktu końcowego HTTP z Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Możesz też uzyskać adres URL, wykonując następujące wywołanie:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Ustaw oczekiwaną metodę HTTP

Domyślnie wyzwalacz żądania oczekuje na żądanie HTTP POST. Można jednak określić inną metodę, która będzie oczekiwać, ale tylko jedną metodę.

1. W wyzwalaczu żądania Otwórz listę **Dodaj nowy parametr** , a następnie wybierz **metodę**, która dodaje tę właściwość do wyzwalacza.

   ![Dodaj właściwość "Method" do wyzwalacza](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. Z listy **Metoda** wybierz inną metodę, która będzie oczekiwać wyzwalacza. Lub można określić metodę niestandardową.

   Na przykład wybierz metodę **Get** , aby można było przetestować adres URL punktu końcowego http później.

   ![Wybierz metodę HTTP, która ma być używana dla wyzwalacza](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Zaakceptuj parametry w adresie URL punktu końcowego

Jeśli chcesz, aby adres URL punktu końcowego akceptował parametry, określ ścieżkę względną w wyzwalaczu. Należy również jawnie [ustawić metodę](#set-method) , której oczekuje żądanie HTTP.

1. W wyzwalaczu żądania Otwórz listę **Dodaj nowy parametr** i wybierz pozycję **ścieżka względna**, która dodaje tę właściwość do wyzwalacza.

   ![Dodaj właściwość "ścieżka względna" do wyzwalacza](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. We właściwości **ścieżka względna** określ ścieżkę względną dla parametru w SCHEMAcie JSON, który ma być akceptowany przez adres URL, na przykład `address/{postalCode}`.

   ![Określ ścieżkę względną dla parametru](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Aby użyć parametru, Znajdź i Dodaj akcję **odpowiedzi** do aplikacji logiki.

   1. W obszarze wyzwalacz żądania wybierz pozycję **nowy krok** > **Dodaj akcję**.

   1. W obszarze **Wybierz akcję**, w polu wyszukiwania wpisz `response` jako filtr.

   1. Z listy Akcje wybierz akcję **odpowiedź** .

1. We właściwości **treści** akcji odpowiedzi Dołącz token, który reprezentuje parametr określony w ścieżce względnej wyzwalacza.

   Załóżmy na przykład, że akcja odpowiedzi powinna zostać zwrócona `Postal Code: {postalCode}`.

   We właściwości **Body** wprowadź `Postal Code: ` spację końcową. Z wyświetlonej listy zawartości dynamicznej wybierz token **KodPocztowy** .

   ![Dodaj określony parametr do treści odpowiedzi](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   Właściwość **Body** zawiera teraz wybrany parametr:

   ![Przykładowa treść odpowiedzi z parametrem](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Zapisz aplikację logiki.

    Adres URL punktu końcowego protokołu HTTP zawiera teraz ścieżkę względną, na przykład:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. Aby przetestować punkt końcowy HTTP, skopiuj i wklej zaktualizowany adres URL do innego okna przeglądarki, ale Zamień `{postalCode}` na `123456`, a następnie naciśnij klawisz ENTER.

   Przeglądarka wyświetla ten tekst:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Wywoływanie aplikacji logiki za pośrednictwem punktu końcowego HTTP

Po utworzeniu punktu końcowego HTTP można wyzwolić aplikację logiki, wysyłając żądanie HTTP `POST` do pełnego adresu URL punktu końcowego. Aplikacje logiki mają wbudowaną obsługę punktów końcowych dostępu bezpośredniego.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokeny wygenerowane na podstawie schematu

Po podaniu schematu JSON w wyzwalaczu żądania projektant aplikacji logiki generuje tokeny dla właściwości w tym schemacie. Następnie można użyć tych tokenów do przekazywania danych za pomocą przepływu pracy aplikacji logiki.

Jeśli na przykład dodasz więcej właściwości, takich jak `"suite"`, do schematu JSON, tokeny dla tych właściwości są dostępne do użycia w kolejnych krokach dla aplikacji logiki. Oto kompletny schemat JSON:

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

Przepływy pracy w aplikacji logiki można zagnieżdżać, dodając inne aplikacje logiki, które mogą odbierać żądania. Aby dołączyć te aplikacje logiki, wykonaj następujące kroki:

1. W kroku, w którym chcesz wywołać inną aplikację logiki, wybierz pozycję **nowy krok** > **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `logic apps` jako filtr. Z listy Akcje wybierz pozycję **Wybierz przepływ pracy Logic Apps**.

   ![Zagnieżdżanie aplikacji logiki w bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Projektant pokazuje kwalifikujące się Aplikacje logiki do wybrania.

1. Wybierz aplikację logiki, która ma zostać wywołana z bieżącej aplikacji logiki.

   ![Wybierz aplikację logiki do wywołania z bieżącej aplikacji logiki](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Odwołuje się do zawartości z przychodzącego żądania

Jeśli typ zawartości żądania przychodzącego to `application/json`, można odwołać się do właściwości w żądaniu przychodzącym. W przeciwnym razie ta zawartość jest traktowana jako pojedyncza jednostka binarna, którą można przekazać do innych interfejsów API. Aby odwołać się do tej zawartości w przepływie pracy aplikacji logiki, należy najpierw skonwertować tę zawartość.

Na przykład jeśli przekazujesz zawartość, która ma `application/xml` typ, możesz użyć [ `@xpath()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#xpath) do przeprowadzenia wyodrębniania XPath lub użyć [ `@json()` wyrażenia](../logic-apps/workflow-definition-language-functions-reference.md#json) do konwertowania XML na notację JSON. Dowiedz się więcej o pracy z obsługiwanymi [typami zawartości](../logic-apps/logic-apps-content-type.md).

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

Czasami chcesz odpowiedzieć na określone żądania wyzwalające aplikację logiki, zwracając zawartość do obiektu wywołującego. Aby skonstruować kod stanu, nagłówek i treść odpowiedzi, użyj akcji odpowiedzi. Ta akcja może pojawić się w dowolnym miejscu w aplikacji logiki, a nie tylko na końcu przepływu pracy. Jeśli aplikacja logiki nie zawiera akcji odpowiedzi, punkt końcowy HTTP odpowie *natychmiast* po **zaakceptowanym stanie 202** .

Aby oryginalny obiekt wywołujący pomyślnie przetworzył odpowiedź, wszystkie wymagane kroki dla odpowiedzi muszą zakończyć się w limicie [limitu czasu żądania](./logic-apps-limits-and-config.md) , chyba że wyzwolona aplikacja logiki jest wywoływana jako zagnieżdżona aplikacja logiki. Jeśli w tym limicie nie zostanie zwrócona odpowiedź, żądanie przychodzące **przekroczy limit czasu** i otrzyma odpowiedź 408.

W przypadku zagnieżdżonych aplikacji logiki nadrzędna aplikacja logiki kontynuuje oczekiwanie na odpowiedź do momentu zakończenia wszystkich kroków, niezależnie od tego, ile czasu jest wymagane.

### <a name="construct-the-response"></a>Konstruowanie odpowiedzi

W treści odpowiedzi można uwzględnić wiele nagłówków i zawartość dowolnego typu. Na przykład nagłówek tej odpowiedzi określa, że typ zawartości odpowiedzi to `application/json` i że treść zawiera wartości właściwości `town` i `postalCode` , w oparciu o schemat JSON opisany wcześniej w tym temacie dla wyzwalacza żądania.

![Podaj zawartość odpowiedzi dla akcji odpowiedzi HTTP](./media/logic-apps-http-endpoint/content-for-response-action.png)

Odpowiedzi mają następujące właściwości:

| Właściwość (Display) | Właściwość (JSON) | Opis |
|--------------------|-----------------|-------------|
| **Kod stanu** | `statusCode` | Kod stanu HTTP, który ma być używany w odpowiedzi dla żądania przychodzącego. Ten kod może być dowolnym prawidłowym kodem stanu zaczynającym się od 2xx, 4xx lub 5xx. Jednak kody stanu 3xx są niedozwolone. |
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

Odp **.: usługa**Azure bezpiecznie generuje adresy URL wywołania zwrotnego aplikacji logiki przy użyciu [sygnatury dostępu współdzielonego (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature). Ten podpis przechodzi przez parametr zapytania i musi być zweryfikowany, aby można było uruchomić aplikację logiki. Platforma Azure generuje podpis przy użyciu unikatowej kombinacji klucza tajnego na aplikację logiki, nazwę wyzwalacza i wykonywaną operację. Jeśli jednak ktoś nie ma dostępu do klucza aplikacji logiki tajnej, nie może wygenerować prawidłowego podpisu.

> [!IMPORTANT]
> W przypadku systemów bezpieczeństwa i wyższych zabezpieczeń zdecydowanie odradzamy wywoływanie aplikacji logiki bezpośrednio z przeglądarki z następujących powodów:
>
> * Klucz dostępu współdzielonego zostanie wyświetlony w adresie URL.
> * Nie można zarządzać zasadami zawartości zabezpieczeń ze względu na domeny udostępnione w ramach klientów Azure Logic Apps.

#### <a name="q-can-i-configure-http-endpoints-further"></a>P: Czy można już skonfigurować punkty końcowe HTTP?

Odp **.: tak**, punkty końcowe http obsługują bardziej zaawansowaną konfigurację za pośrednictwem [usługi Azure API Management](../api-management/api-management-key-concepts.md). Ta usługa oferuje również możliwość spójnego zarządzania wszystkimi interfejsami API, w tym aplikacjami logiki, Konfigurowanie niestandardowych nazw domen, korzystanie z większej liczby metod uwierzytelniania i inne, na przykład:

* [Zmień metodę żądania](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Zmiana segmentów adresu URL żądania](../api-management/api-management-transformation-policies.md#RewriteURL)
* Skonfiguruj domeny API Management w [Azure Portal](https://portal.azure.com/)
* Konfigurowanie zasad do sprawdzania uwierzytelniania podstawowego

## <a name="next-steps"></a>Następne kroki

* [Odbieraj przychodzące wywołania HTTPS i odpowiadaj na nie przy użyciu Azure Logic Apps](../connectors/connectors-native-reqres.md)