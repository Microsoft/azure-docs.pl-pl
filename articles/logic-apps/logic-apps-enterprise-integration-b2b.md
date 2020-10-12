---
title: Komunikaty programu Exchange dla scenariuszy integracji B2B Enterprise
description: Otrzymywanie i wysyłanie komunikatów B2B między partnerami handlowymi w Azure Logic Apps przy użyciu Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: afae49cf6ee44b138a55f58f415fc761308b7894
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542380"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Odbieraj i potwierdzaj wiadomości AS2 B2B przy użyciu Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw

Jeśli masz konto integracji, które definiuje partnerów handlowych i umowy, możesz utworzyć zautomatyzowany przepływ pracy biznesowej dla firm (B2B), który wymienia komunikaty między partnerami handlowymi przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md) z [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md). Azure Logic Apps współpracuje z łącznikami, które obsługują protokoły AS2, X12, EDIFACT i RosettaNet Industry Standard. Możesz również połączyć te łączniki z innymi [łącznikami dostępnymi w Logic Apps](../connectors/apis-list.md), na przykład Salesforce i Office 365 Outlook.

W tym artykule pokazano, jak utworzyć aplikację logiki, która odbiera żądanie HTTP przy użyciu wyzwalacza żądania, dekoduje zawartość komunikatu przy użyciu akcji AS2 i X12, a następnie zwraca odpowiedź przy użyciu akcji odpowiedzi.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pusta aplikacja logiki, dzięki której można utworzyć przepływ pracy B2B przy użyciu wyzwalacza [żądania](../connectors/connectors-native-reqres.md) , po którym następuje następujące akcje:

  * [Dekodowanie AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Warunek](../logic-apps/logic-apps-control-flow-conditional-statement.md), który wysyła [odpowiedź](../connectors/connectors-native-reqres.md) w zależności od tego, czy akcja dekodowania AS2 zakończyła się powodzeniem, czy niepowodzeniem

  * [Dekoduj komunikat X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Jeśli dopiero zaczynasz tworzyć aplikacje Logic Apps, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](./logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure i połączone z aplikacją logiki. Zarówno aplikacja logiki, jak i konto integracji muszą istnieć w tej samej lokalizacji lub regionie platformy Azure.

* Co najmniej dwóch [partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md) , które zostały już zdefiniowane na koncie integracji, wraz z [umowami AS2 i X12](logic-apps-enterprise-integration-agreements.md) dla tych partnerów.

## <a name="add-request-trigger"></a>Dodaj wyzwalacz żądania

Ten przykład korzysta z projektanta aplikacji logiki w Azure Portal, ale można wykonać podobne kroki dla projektanta aplikacji logiki w programie Visual Studio.

1. W [Azure Portal](https://portal.azure.com)Otwórz pustą aplikację logiki w Projektancie aplikacji logiki.

1. W polu wyszukiwania wpisz `when a http request` , a następnie wybierz, **kiedy zostanie odebrane żądanie HTTP** , które ma być używane jako wyzwalacz.

   ![Wybierz wyzwalacz żądania, aby uruchomić przepływ pracy aplikacji logiki](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Pozostaw pole **schematu JSON treści żądania** puste, ponieważ komunikat X12 jest plikiem prostym.

   ![Pozostaw pustą wartość "schemat JSON treści żądania"](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Ten krok generuje **adres URL post protokołu HTTP** , który będzie używany do wysyłania żądania wyzwalającego aplikację logiki. Aby skopiować ten adres URL, wybierz ikonę kopiowania obok adresu URL.

   ![Wygenerowano adres URL dla wyzwalacza żądania, aby można było odbierać wywołania](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Dodaj akcję dekodowania AS2

Teraz Dodaj akcje B2B, których chcesz użyć. W tym przykładzie są stosowane akcje AS2 i X12.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**. Aby ukryć szczegóły wyzwalacza, kliknij pasek tytułu wyzwalacza.

   ![Dodawanie kolejnego kroku do przepływu pracy aplikacji logiki](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz `as2 decode` , a następnie wybierz pozycję **dekodowanie AS2 (v2)**.

   ![Znajdź i wybierz pozycję "dekodowanie AS2 (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Dla właściwości **komunikat do dekodowania** wprowadź dane wejściowe, których akcja AS2 ma zdekodować, czyli `body` zawartość odebraną przez wyzwalacz żądania HTTP. Istnieje wiele sposobów określania tej zawartości jako dane wejściowe, z listy zawartości dynamicznej lub jako wyrażenie:

   * Aby wybrać z listy, która zawiera dostępne wyjściowe wyzwalacza, kliknij wewnątrz **komunikatu, aby zdekodować** . Gdy zostanie wyświetlona lista zawartości dynamicznej, w obszarze **po odebraniu żądania HTTP**wybierz pozycję wartość właściwości **treści** , na przykład:

     ![Wybierz wartość "treść" z wyzwalacza](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Aby wprowadzić wyrażenie odwołujące się do `body` danych wyjściowych wyzwalacza, kliknij wewnątrz **komunikatu, aby zdekodować** . Po wyświetleniu listy zawartości dynamicznej wybierz pozycję **wyrażenie**. W edytorze wyrażeń wprowadź wyrażenie w tym miejscu, a następnie wybierz **przycisk OK**:

     `triggerOutputs()['body']`

     Lub w polu **komunikat do zdekodowania** wpisz bezpośrednio to wyrażenie:

     `@triggerBody()`

     Wyrażenie jest rozpoznawane jako token **treści** .

     ![Rozwiązane dane wyjściowe treści z wyzwalacza](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Dla właściwości **nagłówki wiadomości** wprowadź wszystkie nagłówki wymagane dla akcji AS2, które są opisane przez `headers` zawartość odebraną przez wyzwalacz żądania HTTP.

   Aby wprowadzić wyrażenie odwołujące się do `headers` danych wyjściowych wyzwalacza, kliknij wewnątrz pola **nagłówka komunikatu** . Po wyświetleniu listy zawartości dynamicznej wybierz pozycję **wyrażenie**. W edytorze wyrażeń wprowadź wyrażenie w tym miejscu, a następnie wybierz **przycisk OK**:

   `triggerOutputs()['Headers']`

   Aby uzyskać to wyrażenie do rozpoznania jako ten token, przełączać się między projektantem a widokiem kodu, na przykład:

   ![Rozpoznane nagłówki wyjściowe z wyzwalacza](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Dodaj akcję odpowiedzi dla powiadomienia o otrzymaniu komunikatu

Aby powiadomić partnera handlowego o otrzymaniu wiadomości, można zwrócić odpowiedź zawierającą powiadomienie o AS2 komunikatu (powiadomienia MDN) za pomocą akcji **odpowiedzi** . Po dodaniu tej akcji natychmiast po akcji **dekodowania AS2** , jeśli akcja nie powiedzie się, aplikacja logiki nie będzie kontynuować przetwarzania.

1. W obszarze akcji **dekodowania AS2** wybierz pozycję **nowy krok**.

1. W obszarze **Wybierz akcję**, w polu wyszukiwania wybierz pozycję **wbudowane**. W polu wyszukiwania wpisz `condition`. Na liście **Akcje** wybierz pozycję **Warunek**.

   ![Dodaj akcję "Condition"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Teraz zostanie wyświetlony kształt warunku, w tym ścieżki dotyczące tego, czy warunek jest spełniony, czy nie.

   ![Zrzut ekranu przedstawia kształt warunku z pustymi ścieżkami.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Teraz Określ warunek do obliczenia. W polu **Wybierz wartość** wprowadź następujące wyrażenie:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   W środkowym polu upewnij się, że operacja porównywania jest ustawiona na `is equal to` . W polu po prawej stronie Wprowadź wartość `Expected` . Aby uzyskać wyrażenie do rozpoznania jako ten token, przełączać się między projektantem a widokiem kodu.

   ![Zrzut ekranu przedstawia kształt warunku z dodanym warunkiem.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Teraz Określ odpowiedzi, aby zwrócić, czy Akcja **dekodowania AS2** powiodła się.

   1. W przypadku pomyślnej akcji **dekodowania AS2** w kształcie **Jeśli prawda** wybierz pozycję **Dodaj akcję**. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz `response` , a następnie wybierz pozycję **odpowiedź**.

      ![Znajdź i wybierz akcję "odpowiedź"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Aby uzyskać dostęp do AS2 powiadomienia MDN z danych wyjściowych akcji **dekodowania AS2** , określ następujące wyrażenia:

      * W polu właściwości **nagłówka** akcji **odpowiedzi** wprowadź następujące wyrażenie:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * We właściwości **treść** akcji **odpowiedzi** wprowadź następujące wyrażenie:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Aby uzyskać wyrażenie rozpoznawane jako tokeny, przełączać się między projektantem a widokiem kodu:

      ![Rozpoznano wyrażenie umożliwiające dostęp do AS2 powiadomienia MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. W przypadku niepowodzenia akcji **dekodowania AS2** w kształcie **Jeśli FAŁSZ** wybierz pozycję **Dodaj akcję**. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz `response` , a następnie wybierz pozycję **odpowiedź**. Skonfiguruj akcję **odpowiedź** w celu zwrócenia żądanego stanu i błędu.

1. Zapisz aplikację logiki.

## <a name="add-decode-x12-message-action"></a>Akcja dodawania dekodowania komunikatu X12

1. Teraz Dodaj akcję **Dekoduj X12 komunikat** . W obszarze Akcja **odpowiedzi** wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**w polu wyszukiwania wpisz `x12 decode` , a następnie wybierz opcję **Dekoduj komunikat X12**.

   ![Znajdź i wybierz akcję "Dekoduj komunikat X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Jeśli akcja X12 żąda informacji o połączeniu, podaj nazwę połączenia, wybierz konto integracji, którego chcesz użyć, a następnie wybierz pozycję **Utwórz**.

   ![Utwórz połączenie X12 z kontem integracji](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Teraz Określ dane wejściowe dla akcji X12. W tym przykładzie używane są dane wyjściowe z akcji AS2, która jest zawartością komunikatu, ale należy pamiętać, że ta zawartość jest w formacie obiektu JSON i jest zakodowana przy użyciu kodowania base64. Dlatego należy przekonwertować tę zawartość na ciąg.

   W polu **dekodowanie komunikatu o płaskim pliku X12** wprowadź to wyrażenie, aby przekonwertować dane wyjściowe AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Aby uzyskać wyrażenie do rozpoznania jako ten token, przełączać się między projektantem a widokiem kodu.

    ![Konwertowanie zawartości zakodowanej algorytmem Base64 na ciąg](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Zapisz aplikację logiki.

   Jeśli potrzebujesz dodatkowych kroków dla tej aplikacji logiki, na przykład aby zdekodować zawartość wiadomości i wyprowadzić zawartość w formacie obiektu JSON, Kontynuuj tworzenie aplikacji logiki.

Teraz wszystko jest gotowe do skonfigurowania aplikacji logiki B2B. W aplikacji Real World można przechowywać zdekodowane X12 dane w aplikacji biznesowych lub magazynu danych programu (LOB). Na przykład zobacz następujące artykuły:

* [Łączenie z systemami SAP z usługi Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorowanie i tworzenie plików SFTP oraz zarządzanie nimi za pomocą protokołu SSH i usługi Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Aby połączyć własne aplikacje LOB i używać tych interfejsów API w aplikacji logiki, możesz dodać więcej akcji lub [napisać niestandardowe interfejsy API](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Następne kroki

* [Odbieraj przychodzące wywołania HTTPS i odpowiadaj na nie](../connectors/connectors-native-reqres.md)
* [Komunikaty programu Exchange AS2 na potrzeby integracji z usługą B2B Enterprise](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Komunikaty programu Exchange X12 na potrzeby integracji z usługą B2B Enterprise](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
