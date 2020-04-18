---
title: Wywoływanie punktów końcowych usługi przy użyciu protokołu HTTP lub HTTPS
description: Wysyłanie wychodzących żądań HTTP lub HTTPS do punktów końcowych usługi z aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 6c52f2df34faf441ab70b48b11bbc393ebcecb65
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617611"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Wywoływanie punktów końcowych usługi za pośrednictwem protokołu HTTP lub HTTPS z aplikacji logiki Azure

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanego wyzwalacza lub akcji HTTP można tworzyć zautomatyzowane zadania i przepływy pracy, które wysyłają żądania do punktów końcowych usługi za pośrednictwem protokołu HTTP lub HTTPS. Na przykład można monitorować punkt końcowy usługi dla witryny sieci Web, sprawdzając ten punkt końcowy w określonym harmonogramie. Gdy określone zdarzenie dzieje się w tym punkcie końcowym, takich jak witryny sieci Web będzie w dół, zdarzenie wyzwala przepływu pracy aplikacji logiki i uruchamia akcje w tym przepływie pracy. Jeśli chcesz odbierać przychodzące wywołania HTTPS i odpowiadać na nie, użyj wbudowanego [wyzwalacza żądania lub akcji Odpowiedź](../connectors/connectors-native-reqres.md).

> [!NOTE]
> Na podstawie możliwości docelowego punktu końcowego łącznik HTTP obsługuje usługi TLS (Transport Layer Security) w wersjach 1.0, 1.1 i 1.2. Logic Apps negocjuje z punktem końcowym przy użyciu najwyższej obsługiwanej wersji możliwe. Na przykład jeśli punkt końcowy obsługuje 1.2, łącznik używa najpierw 1.2. W przeciwnym razie łącznik używa następnej najwyższej obsługiwanej wersji.

Aby sprawdzić lub *sondować* punkt końcowy zgodnie z harmonogramem cyklicznym, [dodaj wyzwalacz HTTP](#http-trigger) jako pierwszy krok w przepływie pracy. Za każdym razem, gdy wyzwalacz sprawdza punkt końcowy, wyzwalacz wywołuje lub wysyła *żądanie* do punktu końcowego. Odpowiedź punktu końcowego określa, czy przepływ pracy aplikacji logiki jest uruchamiany. Wyzwalacz przekazuje dowolną zawartość z odpowiedzi punktu końcowego do akcji w aplikacji logiki.

Aby wywołać punkt końcowy z dowolnego miejsca w przepływie pracy, [dodaj akcję HTTP](#http-action). Odpowiedź punktu końcowego określa sposób uruchamiania pozostałych akcji przepływu pracy.

> [!IMPORTANT]
> Jeśli wyzwalacz lub akcja HTTP zawiera te nagłówki, aplikacje logiki usuwa te nagłówki z wygenerowanego komunikatu żądania bez wyświetlania ostrzeżenia lub błędu:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`z tymi wyjątkami: `Content-Disposition`, `Content-Encoding`, i`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Chociaż aplikacje logiki nie powstrzyma cię od zapisywania aplikacji logiki, które używają wyzwalacza HTTP lub akcji z tymi nagłówkami, aplikacje logiki ignoruje te nagłówki.

W tym artykule pokazano, jak dodać wyzwalacz lub akcję HTTP do przepływu pracy aplikacji logiki.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL docelowego punktu końcowego, który chcesz wywołać

* Podstawowa wiedza na temat [tworzenia aplikacji logicznych](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, z której chcesz wywołać docelowy punkt końcowy. Aby rozpocząć od wyzwalacza HTTP, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. W tym przykładzie użyto wyzwalacza HTTP jako pierwszego kroku.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Dodawanie wyzwalacza HTTP

Ten wbudowany wyzwalacz wywołuje http do określonego adresu URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz pustą aplikację logiki w logice App Designer.

1. W polu wyszukiwania projektanta wybierz pozycję **Wbudowane**. W polu wyszukiwania `http` wprowadź jako filtr. Z listy **Wyzwalacze** wybierz wyzwalacz **HTTP.**

   ![Wybierz wyzwalacz HTTP](./media/connectors-native-http/select-http-trigger.png)

   W tym przykładzie zmienia nazwę wyzwalacza na "Wyzwalacz HTTP", tak aby krok miał bardziej opisową nazwę. Ponadto w przykładzie później dodaje akcję HTTP, a obie nazwy muszą być unikatowe.

1. Podaj wartości [parametrów wyzwalacza HTTP,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) które chcesz uwzględnić w wywołaniu docelowego punktu końcowego. Skonfiguruj cykl, aby sprawdzić, jak często wyzwalacz ma sprawdzać docelowy punkt końcowy.

   ![Wprowadź parametry wyzwalacza HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Jeśli wybierzesz typ uwierzytelniania inny niż **Brak,** ustawienia uwierzytelniania różnią się w zależności od wyboru. Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla protokołu HTTP, zobacz następujące tematy:

   * [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Uwierzytelnij dostęp do zasobów z tożsamościami zarządzanymi](../logic-apps/create-managed-service-identity.md)

1. Aby dodać inne dostępne parametry, otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki z akcji, które są uruchamiane po uruchomieniu wyzwalacza.

1. Po zakończeniu pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Dodawanie akcji HTTP

Ta wbudowana akcja wywołuje HTTP określony adres URL dla punktu końcowego i zwraca odpowiedź.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz aplikację logiki w logic app designer.

   W tym przykładzie użyto wyzwalacza HTTP jako pierwszego kroku.

1. W obszarze kroku, w którym chcesz dodać akcję HTTP, wybierz pozycję **Nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `http` wprowadź jako filtr. Z listy **Akcje** wybierz akcję **HTTP.**

   ![Wybierz akcję HTTP](./media/connectors-native-http/select-http-action.png)

   W tym przykładzie zmienia nazwę akcji na "Akcja HTTP", dzięki czemu krok ma bardziej opisową nazwę.

1. Podaj wartości [parametrów akcji HTTP,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) które chcesz uwzględnić w wywołaniu docelowego punktu końcowego.

   ![Wprowadź parametry akcji HTTP](./media/connectors-native-http/http-action-parameters.png)

   Jeśli wybierzesz typ uwierzytelniania inny niż **Brak,** ustawienia uwierzytelniania różnią się w zależności od wyboru. Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla protokołu HTTP, zobacz następujące tematy:

   * [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Uwierzytelnij dostęp do zasobów z tożsamościami zarządzanymi](../logic-apps/create-managed-service-identity.md)

1. Aby dodać inne dostępne parametry, otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

1. Po zakończeniu pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="content-with-multipartform-data-type"></a>Zawartość z wieloczęściowym/typu danych formularza

Aby obsłużyć zawartość wpisywalną `multipart/form-data` w żądaniach `$content-type` HTTP, można dodać obiekt JSON, który zawiera atrybuty i `$multipart` atrybuty do treści żądania HTTP przy użyciu tego formatu.

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

Załóżmy na przykład, że masz aplikację logiki, która wysyła żądanie HTTP POST dla pliku `multipart/form-data` programu Excel do witryny sieci Web przy użyciu interfejsu API tej witryny, który obsługuje ten typ. Oto jak może wyglądać ta akcja:

![Wieloczęściowe dane formularza](./media/connectors-native-http/http-action-multipart.png)

Oto ten sam przykład, który pokazuje definicję JSON akcji HTTP w podstawowej definicji przepływu pracy:

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

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji na temat parametrów wyzwalacza i akcji, zobacz następujące sekcje:

* [Parametry wyzwalacza HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametry akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Szczegóły wydruku

Oto więcej informacji na temat wyjść z wyzwalacza lub akcji HTTP, która zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| Nagłówki | obiekt | Nagłówki z żądania |
| body | obiekt | Obiekt JSON | Obiekt z zawartością treści z żądania |
| kod stanu | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowane |
| 400 | Zła prośba |
| 401 | Brak autoryzacji |
| 403 | Forbidden |
| 404 | Nie znaleziono |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
