---
title: Odbieraj wywołania i odpowiadaj na nie przy użyciu protokołu HTTPS
description: Obsługa przychodzących żądań HTTPS z usług zewnętrznych przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063016"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Odbieranie przychodzących żądań HTTPS i odpowiadanie na nie w Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanej akcji wyzwalacza żądania i odpowiedzi można tworzyć automatyczne zadania i przepływy pracy, które mogą odbierać przychodzące żądania za pośrednictwem protokołu HTTPS. Aby zamiast tego wysyłać żądania wychodzące, należy użyć wbudowanego [wyzwalacza http lub akcji http](../connectors/connectors-native-http.md).

Na przykład możesz mieć aplikację logiki:

* Odbieraj żądania HTTPS dotyczące danych i odpowiadaj na nie w lokalnej bazie danych.

* Wyzwalanie przepływu pracy po wystąpieniu zewnętrznego zdarzenia elementu webhook.

* Odbieraj i odpowiadaj na wywołanie HTTPS z innej aplikacji logiki.

W tym artykule pokazano, jak używać wyzwalacza żądań i akcji odpowiedzi, aby aplikacja logiki mogła odbierać wywołania przychodzące i odpowiadać na nie.

Aby uzyskać więcej informacji o zabezpieczeniach, autoryzacji i szyfrowaniu wywołań przychodzących do aplikacji logiki, takiej jak [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), wcześniej znanej jako Secure SOCKETS Layer (SSL), [Azure Active Directory otwarte uwierzytelnianie (Azure AD OAuth)](../active-directory/develop/index.yml), Uwidacznianie aplikacji logiki za pomocą usługi Azure API Management lub ograniczanie adresów IP, które pochodzą z wywołań przychodzących, zobacz [bezpieczny dostęp i dostęp do danych dla wywołań przychodzących do wyzwalaczy opartych na żądaniach](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [zarejestrować się w celu uzyskania bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Dodaj wyzwalacz żądania

Ten wbudowany wyzwalacz tworzy ręcznie wywołujący punkt końcowy, który może obsługiwać *tylko* żądania przychodzące za pośrednictwem protokołu HTTPS. Gdy wywołujący wysyła żądanie do tego punktu końcowego, [wyzwalacz żądania](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) uruchamia i uruchamia aplikację logiki. Aby uzyskać więcej informacji o sposobie wywoływania tego wyzwalacza, zobacz [wywoływanie, wyzwalanie lub zagnieżdżanie przepływów pracy za pomocą punktów końcowych https w Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Aplikacja logiki utrzymuje otwarte żądanie przychodzące tylko przez [ograniczony czas](../logic-apps/logic-apps-limits-and-config.md#http-limits). Przy założeniu, że aplikacja logiki zawiera [akcję odpowiedzi](#add-response), jeśli aplikacja logiki nie wyśle odpowiedzi z powrotem do obiektu wywołującego po upływie tego czasu, aplikacja logiki zwróci `504 GATEWAY TIMEOUT` stan do obiektu wywołującego. Jeśli aplikacja logiki nie zawiera akcji odpowiedzi, aplikacja logiki natychmiast zwróci `202 ACCEPTED` stan do obiektu wywołującego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Tworzenia pustej aplikacji logiki.

1. Gdy zostanie otwarty projektant aplikacji logiki, w polu wyszukiwania wprowadź `http request` jako filtr. Z listy Wyzwalacze wybierz wyzwalacz **po odebraniu żądania HTTP** .

   ![Wybierz wyzwalacz żądania](./media/connectors-native-reqres/select-request-trigger.png)

   Wyzwalacz żądania zawiera następujące właściwości:

   ![Wyzwalacz żądania](./media/connectors-native-reqres/request-trigger.png)

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagane | Opis |
   |---------------|--------------------|----------|-------------|
   | **ADRES URL POST PROTOKOŁU HTTP** | dawaj | Tak | Adres URL punktu końcowego, który jest generowany po zapisaniu aplikacji logiki i jest używany do wywoływania aplikacji logiki |
   | **Schemat JSON treści żądania** | `schema` | Nie | Schemat JSON, który opisuje właściwości i wartości w treści żądania przychodzącego |
   |||||

1. W polu **schemat JSON treści żądania** opcjonalnie wprowadź schemat JSON, który opisuje treść w żądaniu przychodzącym, na przykład:

   ![Przykładowy schemat JSON](./media/connectors-native-reqres/provide-json-schema.png)

   Projektant używa tego schematu do generowania tokenów dla właściwości w żądaniu. Dzięki temu aplikacja logiki może analizować, wykorzystywać i przekazywać dane z żądania za pośrednictwem wyzwalacza do przepływu pracy.

   Oto przykładowy schemat:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Po wprowadzeniu schematu JSON Projektant wyświetla przypomnienie, aby dołączyć `Content-Type` nagłówek do żądania i ustawić wartość tego nagłówka `application/json` . Aby uzyskać więcej informacji, zobacz temat [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).

   ![Przypomnienie o uwzględnieniu nagłówka "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Oto, jak wygląda ten nagłówek w formacie JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Aby wygenerować schemat JSON, który jest oparty na oczekiwanym ładunku (danych), można użyć narzędzia, takiego jak [JSONSchema.NET](https://jsonschema.net)lub wykonać następujące czynności:

   1. W wyzwalaczu żądania wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

      ![Zrzut ekranu z wybranym elementem "Użyj przykładowego ładunku do wygenerowania schematu"](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Wprowadź przykładowy ładunek i wybierz pozycję **gotowe**.

      ![Wprowadź przykładowy ładunek, aby wygenerować schemat](./media/connectors-native-reqres/enter-payload.png)

      Oto przykład ładunku:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Aby sprawdzić, czy wywołanie przychodzące ma treść żądania zgodną z określonym schematem, wykonaj następujące kroki:

   1. Na pasku tytułu wyzwalacza żądania wybierz przycisk wielokropka (**...**).

   1. W ustawieniach wyzwalacza Włącz **Sprawdzanie poprawności schematu** i wybierz pozycję **gotowe**.

      Jeśli treść żądania wywołania przychodzącego nie jest zgodna ze schematem, wyzwalacz zwróci `HTTP 400 Bad Request` błąd.

1. Aby określić dodatkowe właściwości, Otwórz listę **Dodaj nowy parametr** i wybierz parametry, które chcesz dodać.

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagane | Opis |
   |---------------|--------------------|----------|-------------|
   | **Metoda** | `method` | Nie | Metoda, która musi być używana przez żądanie przychodzące do wywoływania aplikacji logiki |
   | **Ścieżka względna** | `relativePath` | Nie | Ścieżka względna parametru, który może zostać zaakceptowany przez adres URL punktu końcowego aplikacji logiki |
   |||||

   Ten przykład dodaje właściwość **metody** :

   ![Dodaj parametr metody](./media/connectors-native-reqres/add-parameters.png)

   Właściwość **Metoda** zostanie wyświetlona w wyzwalaczu, aby można było wybrać metodę z listy.

   ![Select — metoda](./media/connectors-native-reqres/select-method.png)

1. Teraz Dodaj kolejną akcję w następnym kroku w przepływie pracy. W obszarze wyzwalacza wybierz pozycję **Następny krok** , aby znaleźć akcję, którą chcesz dodać.

   Na przykład można odpowiedzieć na żądanie, [dodając akcję odpowiedzi](#add-response), której można użyć do zwrócenia dostosowanej odpowiedzi i opisanej w dalszej części tego tematu.

   Aplikacja logiki utrzymuje otwarte żądanie przychodzące tylko przez [ograniczony czas](../logic-apps/logic-apps-limits-and-config.md#http-limits). Przy założeniu, że przepływ pracy aplikacji logiki zawiera akcję odpowiedzi, jeśli aplikacja logiki nie zwróci odpowiedzi po upływie tego czasu, aplikacja logiki zwróci `504 GATEWAY TIMEOUT` obiekt wywołujący. W przeciwnym razie, jeśli aplikacja logiki nie zawiera akcji odpowiedzi, aplikacja logiki natychmiast zwróci `202 ACCEPTED` odpowiedź do obiektu wywołującego.

1. Gdy skończysz, Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok generuje adres URL służący do wysyłania żądania wyzwalającego aplikację logiki. Aby skopiować ten adres URL, wybierz ikonę kopiowania obok adresu URL.

   ![Adres URL używany do wyzwalania aplikacji logiki](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Jeśli chcesz uwzględnić skrót lub symbol funta ( **#** ) w identyfikatorze URI podczas wykonywania wywołania wyzwalacza żądania, Użyj tej zakodowanej wersji: `%25%23`

1. Aby przetestować aplikację logiki, Wyślij żądanie HTTP do wygenerowanego adresu URL.

   Na przykład można użyć narzędzia, takiego jak [ogłośer](https://www.getpostman.com/) , aby wysłać żądanie HTTP. Aby uzyskać więcej informacji na temat podstawowej definicji JSON wyzwalacza i sposobu wywoływania tego wyzwalacza, zobacz te tematy, [Typ wyzwalacza żądania](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) oraz [przepływy pracy wywołania, wyzwalacza lub zagnieżdżania z punktami końcowymi http w Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

Aby uzyskać więcej informacji o zabezpieczeniach, autoryzacji i szyfrowaniu wywołań przychodzących do aplikacji logiki, takiej jak [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), wcześniej znanej jako Secure SOCKETS Layer (SSL), [Azure Active Directory otwarte uwierzytelnianie (Azure AD OAuth)](../active-directory/develop/index.yml), Uwidacznianie aplikacji logiki za pomocą usługi Azure API Management lub ograniczanie adresów IP, które pochodzą z wywołań przychodzących, zobacz [bezpieczny dostęp i dostęp do danych dla wywołań przychodzących do wyzwalaczy opartych na żądaniach](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)

## <a name="trigger-outputs"></a>Wyjściowe wyzwalacza

Poniżej znajduje się więcej informacji na temat danych wyjściowych wyzwalacza żądania:

| Nazwa właściwości JSON | Typ danych | Opis |
|--------------------|-----------|-------------|
| `headers` | Obiekt | Obiekt JSON, który opisuje nagłówki z żądania |
| `body` | Obiekt | Obiekt JSON, który opisuje zawartość treści z żądania |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Dodaj akcję odpowiedzi

Korzystając z wyzwalacza żądania do obsługi żądań przychodzących, można modelować odpowiedź i wysyłać do niej wyniki z powrotem do obiektu wywołującego za pomocą wbudowanej [akcji odpowiedzi](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action). Akcja odpowiedzi może być używana *tylko* z wyzwalaczem żądania. Ta kombinacja z wyzwalaczem żądania i akcją odpowiedzi tworzy [wzorzec żądanie-odpowiedź](https://en.wikipedia.org/wiki/Request%E2%80%93response). Z wyjątkiem wewnątrz pętli Foreach i do pętli, a gałęzie równoległe, można dodać akcję odpowiedzi w dowolnym miejscu w przepływie pracy.

> [!IMPORTANT]
> Jeśli akcja odpowiedzi zawiera te nagłówki, Logic Apps usuwa te nagłówki z wygenerowanego komunikatu odpowiedzi bez wyświetlania ostrzeżenia lub błędu:
>
> * `Allow`
> * `Content-*` nagłówki z wyjątkiem `Content-Disposition` , `Content-Encoding` i, `Content-Type` gdy używane są operacje post i Put, ale nie są uwzględniane podczas operacji pobierania
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Mimo że Logic Apps nie zatrzyma zapisywania aplikacji logiki, które mają akcję odpowiedzi z tymi nagłówkami, Logic Apps ignoruje te nagłówki.

1. W Projektancie aplikacji logiki w kroku, w którym chcesz dodać akcję odpowiedzi, wybierz pozycję **nowy krok**.

   Na przykład przy użyciu wyzwalacza żądania z wcześniejszych wersji:

   ![Dodaj nowy krok](./media/connectors-native-reqres/add-response.png)

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję** w polu wyszukiwania wprowadź `response` jako filtr, a następnie wybierz akcję **odpowiedź** .

   ![Wybierz akcję odpowiedzi](./media/connectors-native-reqres/select-response-action.png)

   Wyzwalacz żądania jest zwinięty w tym przykładzie dla uproszczenia.

1. Dodaj wszystkie wartości, które są wymagane dla komunikatu odpowiedzi.

   W niektórych polach kliknięcie wewnątrz nich spowoduje otwarcie listy zawartości dynamicznej. Następnie można wybrać tokeny reprezentujące dostępne dane wyjściowe z poprzednich kroków w przepływie pracy. Właściwości ze schematu określonego w poprzednim przykładzie pojawiają się teraz na liście zawartości dynamicznej.

   Na przykład dla pola **nagłówki** Dołącz `Content-Type` jako nazwę klucza i ustaw wartość klucza na `application/json` jak wspomniano wcześniej w tym temacie. Dla pola **treść** możesz wybrać pozycję wyzwalanie danych wyjściowych z listy zawartości dynamicznej.

   ![Szczegóły akcji odpowiedzi](./media/connectors-native-reqres/response-details.png)

   Aby wyświetlić nagłówki w formacie JSON, wybierz polecenie **Przełącz do widoku tekstu**.

   ![Nagłówki — Przełącz do widoku tekstu](./media/connectors-native-reqres/switch-to-text-view.png)

   Poniżej znajduje się więcej informacji na temat właściwości, które można ustawić w akcji odpowiedzi.

   | Nazwa właściwości | Nazwa właściwości JSON | Wymagane | Opis |
   |---------------|--------------------|----------|-------------|
   | **Kod stanu** | `statusCode` | Tak | Kod stanu do zwrócenia w odpowiedzi |
   | **Nagłówki** | `headers` | Nie | Obiekt JSON, który opisuje jeden lub więcej nagłówków do uwzględnienia w odpowiedzi |
   | **Treść** | `body` | Nie | Treść odpowiedzi |
   |||||

1. Aby określić dodatkowe właściwości, takie jak schemat JSON dla treści odpowiedzi, Otwórz listę **Dodaj nowy parametr** i wybierz parametry, które chcesz dodać.

1. Gdy skończysz, Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* [Bezpieczny dostęp i dostęp do danych dla wywołań przychodzących do wyzwalaczy opartych na żądaniach](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Łączniki dla usługi Logic Apps](../connectors/apis-list.md)
