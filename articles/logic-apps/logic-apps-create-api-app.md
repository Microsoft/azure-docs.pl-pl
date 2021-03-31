---
title: Tworzenie internetowych interfejsów API & interfejsów API REST dla Azure Logic Apps
description: Tworzenie internetowych interfejsów API & interfejsów API REST, które umożliwiają wywoływanie interfejsów API, usług lub systemów na potrzeby integracji systemu w programie Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073220"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Tworzenie niestandardowych interfejsów API, które można wywołać z Azure Logic Apps

Chociaż Azure Logic Apps oferuje [setki łączników](../connectors/apis-list.md) , których można używać w przepływach pracy aplikacji logiki, można wywołać interfejsy API, systemy i usługi, które nie są dostępne jako łączniki. Możesz tworzyć własne interfejsy API, które udostępniają akcje i wyzwalacze do użycia w usłudze Logic Apps. Oto inne powody, dla których warto utworzyć własne interfejsy API, które można wywołać z przepływów pracy aplikacji logiki:

* Zwiększ aktualną przepływy pracy integracji z integracją systemu i integracji danych.
* Pomóż klientom używać usługi do zarządzania zadaniami profesjonalnymi lub osobistymi.
* Rozwiń zasięg, wykrywalność i użycie dla swojej usługi.

W istocie łączniki to interfejsy API sieci Web, które używają protokołu REST dla interfejsów podłączanych, [format metadanych struktury Swagger](https://swagger.io/specification/) dla dokumentacji i kod JSON w formacie wymiany danych. Ponieważ łączniki są interfejsami API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takiego jak .NET, Java, Python lub Node.js, do tworzenia łączników. Możesz również hostować swoje interfejsy API na [Azure App Service](../app-service/overview.md)— oferta platformy jako usługi (PaaS), która zapewnia jeden z najlepszych, najłatwiejszych i najbardziej skalowalnych sposobów obsługi interfejsu API. 

Aby niestandardowe interfejsy API działały z usługą Logic Apps, interfejs API może dostarczyć [*Akcje*](./logic-apps-overview.md#logic-app-concepts) wykonujące określone zadania w przepływach pracy aplikacji logiki. Interfejs API może również działać jako [*wyzwalacz*](./logic-apps-overview.md#logic-app-concepts) , który uruchamia przepływ pracy aplikacji logiki, gdy nowe dane lub zdarzenie spełniają określony warunek. W tym temacie opisano typowe wzorce, które można wykonać, aby utworzyć akcje i Wyzwalacze w interfejsie API w oparciu o zachowanie, które ma być używane przez interfejs API.

Interfejsy API można hostować w [Azure App Service](../app-service/overview.md), oferującej platformę jako usługę (PaaS), która zapewnia wysoce skalowalne i łatwe hosting interfejsów API.

> [!TIP] 
> Chociaż można wdrożyć interfejsy API jako aplikacje sieci Web, warto rozważyć wdrożenie interfejsów API jako aplikacji interfejsu API, co może ułatwić wykonywanie zadań podczas kompilowania, hostowania i używania interfejsów API w chmurze i lokalnie. Nie trzeba zmieniać żadnego kodu w interfejsach API — wystarczy wdrożyć kod w aplikacji interfejsu API. Zapoznaj się z przykładem, jak tworzyć aplikacje interfejsu API utworzone przy użyciu następujących języków: 
> 
> * [ASP.NET](../app-service/quickstart-dotnetcore.md). 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> Aby zapoznać się z przykładami aplikacji interfejsu API utworzonymi dla usługi Logic Apps, odwiedź [Azure Logic Apps repozytorium GitHub](https://github.com/logicappsio).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Jak niestandardowe interfejsy API różnią się od łączników niestandardowych?

Niestandardowe interfejsy API i [Łączniki niestandardowe](../logic-apps/custom-connector-overview.md) to interfejsy API sieci Web, które używają protokołu REST dla interfejsów podłączanych, [format metadanych struktury Swagger](https://swagger.io/specification/) dla dokumentacji i kod JSON w formacie wymiany danych. Ponieważ te interfejsy API i łączniki są interfejsami API REST, które komunikują się za pośrednictwem punktów końcowych HTTP, można użyć dowolnego języka, takiego jak .NET, Java, Python lub Node.js, do tworzenia niestandardowych interfejsów API i łączników.

Niestandardowe interfejsy API umożliwiają wywoływanie interfejsów API, które nie są łącznikami i zapewniają punkty końcowe, które można wywoływać za pomocą protokołu HTTP + Swagger, platformy Azure API Management lub App Services. Łączniki niestandardowe działają podobnie jak niestandardowe interfejsy API, ale mają również następujące atrybuty:

* Zarejestrowano jako zasoby łącznika Logic Apps na platformie Azure.
* Pojawia się z ikonami obok łączników zarządzanych przez firmę Microsoft w projektancie Logic Apps.
* Dostępny tylko dla autorów i użytkowników aplikacji logiki łączników, którzy mają tę samą Azure Active Directory dzierżawę i subskrypcję platformy Azure w regionie, w którym są wdrażane aplikacje logiki.

Można również wyznaczyć zarejestrowane łączniki do certyfikacji firmy Microsoft. Ten proces sprawdza, czy zarejestrowane łączniki spełniają kryteria do użytku publicznego i udostępniają te łączniki użytkownikom w usłudze w celu automatyzacji i zarządzania aplikacjami.

Aby uzyskać więcej informacji na temat łączników niestandardowych, zobacz. 

* [Przegląd łączników niestandardowych](../logic-apps/custom-connector-overview.md)
* [Tworzenie łączników niestandardowych z interfejsów API sieci Web](/connectors/custom-connectors/create-web-api-connector)
* [Rejestrowanie łączników niestandardowych w Azure Logic Apps](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Przydatne narzędzia

Niestandardowy interfejs API działa najlepiej z usługą Logic Apps, gdy interfejs API zawiera również [dokument struktury Swagger](https://swagger.io/specification/) opisujący operacje i parametry interfejsu API.
Wiele bibliotek, takich jak [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), może automatycznie generować plik Swagger. Aby dodać adnotację do pliku struktury Swagger pod kątem nazw wyświetlanych, typów właściwości i tak dalej, można również użyć [Trex](https://github.com/nihaue/TRex) , aby plik struktury Swagger działał poprawnie z aplikacjami logiki.

<a name="actions"></a>

## <a name="action-patterns"></a>Wzorce akcji

Aby Aplikacje logiki wykonywały zadania, niestandardowy interfejs API powinien udostępniać [*Akcje*](./logic-apps-overview.md#logic-app-concepts). Każda operacja w interfejsie API jest mapowana na akcję. Podstawowa akcja to kontroler, który akceptuje żądania HTTP i zwraca odpowiedzi HTTP. Na przykład aplikacja logiki wysyła żądanie HTTP do aplikacji sieci Web lub aplikacji interfejsu API. Aplikacja zwróci odpowiedź HTTP wraz z zawartością, którą może przetworzyć aplikacja logiki.

W przypadku akcji standardowej można napisać metodę żądania HTTP w interfejsie API i opisać tę metodę w pliku struktury Swagger. Następnie można wywołać interfejs API bezpośrednio z [akcją http](../connectors/connectors-native-http.md) lub akcją [protokołu HTTP + Swagger](../connectors/connectors-native-http-swagger.md) . Domyślnie odpowiedzi muszą być zwracane w ramach [limitu czasu żądania](./logic-apps-limits-and-config.md). 

![Wzorzec akcji standardowej](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Aby aplikacja logiki czekała na zakończenie wykonywania dłuższych zadań przez interfejs API, interfejs API może postępować zgodnie z [asynchronicznym wzorcem sondowania](#async-pattern) lub [wzorcem asynchronicznego elementu webhook](#webhook-actions) opisanym w tym temacie. Dla analogowej, która pomaga wizualizować te wzorce, przypuśćmy proces porządkowania niestandardowego ciastka z piekarni. Wzorzec sondowania odzwierciedla zachowanie w przypadku wywołania piekarni co 20 minut, aby sprawdzić, czy ciasto jest gotowe. Wzorzec elementu webhook odzwierciedla zachowanie w przypadku, gdy piekarni prosi o numer telefonu, dzięki czemu mogą wywoływać Cię, gdy ciasto jest gotowe.

Aby zapoznać się z przykładami, odwiedź [Logic Apps repozytorium GitHub](https://github.com/logicappsio). Dowiedz się więcej o [zliczaniu użycia akcji](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Wykonywanie długotrwałych zadań z wzorcem akcji sondowania

Aby interfejs API wykonywał zadania, które mogą być uruchamiane dłużej niż [limit czasu żądania](./logic-apps-limits-and-config.md), można użyć wzorca sondowania asynchronicznego. Ten wzorzec ma działanie interfejsu API w osobnym wątku, ale utrzymuje aktywne połączenie z aparatem Logic Apps. W ten sposób aplikacja logiki nie przekroczy limitu czasu ani nie przejdzie do następnego kroku w przepływie pracy, zanim interfejs API zakończy pracę.

Oto ogólny wzorzec:

1. Upewnij się, że aparat wie, że interfejs API zaakceptował żądanie i uruchomił pracę.
2. Gdy aparat wykonuje kolejne żądania dla stanu zadania, pozwól aparatowi znać czas zakończenia zadania przez interfejs API.
3. Zwróć odpowiednie dane do aparatu, aby umożliwić kontynuowanie przepływu pracy aplikacji logiki.

<a name="bakery-polling-action"></a> Teraz stosujemy poprzednią analogową piekarni do wzorca sondowania i Wyobraź sobie, że wywołasz piekarni i porządkuje niestandardowe ciastko do dostarczenia. Proces tworzenia ciastka trwa i nie chce czekać na telefon, gdy piekarni działa na ciastku. Piekarni potwierdza zamówienie i jest wywoływana co 20 minut dla stanu ciastka. Po upływie 20 minut wywołasz piekarni, ale informuje o tym, że ciasto nie zostało zrobione i że należy wywołać inne 20 minut. Ten proces jest kontynuowany do momentu wywołania, a piekarni informuje o tym, że zamówienie jest gotowe i dostarcza Twoje ciastko. 

Teraz zmapujmy ten wzorzec sondowania. Piekarni reprezentuje niestandardowy interfejs API, podczas gdy klient ciastka reprezentuje silnik Logic Apps. Gdy aparat wywołuje interfejs API przy użyciu żądania, interfejs API potwierdza żądanie i reaguje na przedział czasu, gdy aparat może sprawdzić stan zadania. Aparat kontynuuje sprawdzanie stanu zadania do momentu, gdy interfejs API odpowie, że zadanie zostało wykonane i zwraca dane do aplikacji logiki, która następnie kontynuuje przepływ pracy. 

![Wzorzec akcji sondowania](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Poniżej przedstawiono kroki, które należy wykonać w przypadku interfejsu API, opisane w perspektywie interfejsu API:

1. Gdy interfejs API pobiera żądanie HTTP, aby rozpocząć pracę, należy natychmiast zwrócić `202 ACCEPTED` odpowiedź HTTP z `location` nagłówkiem opisanym w dalszej części tego kroku. Ta odpowiedź umożliwia aparatowi Logic Apps wiadomo, że interfejs API otrzymał żądanie, zaakceptował ładunek żądania (dane wejściowe) i jest teraz przetwarzany. 
   
   `202 ACCEPTED`Odpowiedź powinna zawierać następujące nagłówki:
   
   * *Wymagane*: `location` nagłówek określający ścieżkę bezwzględną do adresu URL, pod którym aparat Logic Apps może sprawdzić stan zadania interfejsu API

   * *Opcjonalnie*: `retry-after` nagłówek określający liczbę sekund oczekiwania przez aparat przed sprawdzeniem `location` adresu URL dla stanu zadania. 

     Domyślnie aparat sprawdza co 20 sekund. Aby określić inny interwał, Dołącz `retry-after` Nagłówek i liczbę sekund do następnego sondowania.

2. Po upływie określonego czasu aparat Logic Apps sonduje `location` adres URL w celu sprawdzenia stanu zadania. Interfejs API powinien wykonać te testy i zwrócić następujące odpowiedzi:
   
   * Jeśli zadanie zostanie wykonane, zwróć `200 OK` odpowiedź HTTP wraz z ładunkiem odpowiedzi (dane wejściowe dla następnego kroku).

   * Jeśli zadanie jest nadal przetwarzane, zwróć kolejną odpowiedź HTTP `202 ACCEPTED` , ale z takimi samymi nagłówkami jak oryginalna odpowiedź.

Gdy interfejs API jest zgodny z tym wzorcem, nie trzeba wykonywać żadnych czynności w definicji przepływu pracy aplikacji logiki, aby kontynuować sprawdzanie stanu zadania. Gdy aparat otrzymuje `202 ACCEPTED` odpowiedź HTTP i prawidłowy `location` nagłówek, aparat szanuje wzorzec asynchroniczny i sprawdza `location` nagłówek do momentu, gdy interfejs API zwróci odpowiedź różną od 202.

> [!TIP]
> Aby zapoznać się z przykładowym wzorcem asynchronicznym, zapoznaj się z tym przykładem asynchronicznej [odpowiedzi kontrolera w](https://github.com/logicappsio/LogicAppsAsyncResponseSample)witrynie

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Wykonywanie długotrwałych zadań z wzorcem akcji elementu webhook

Alternatywnie można użyć wzorca elementu webhook do długotrwałych zadań i przetwarzania asynchronicznego. Ten wzorzec ma wstrzymać działanie aplikacji logiki i poczekać na "wywołanie zwrotne" z interfejsu API, aby zakończyć przetwarzanie przed kontynuowaniem przepływu pracy. To wywołanie zwrotne to wpis HTTP, który wysyła komunikat do adresu URL, gdy wystąpi zdarzenie. 

<a name="bakery-webhook-action"></a> Teraz Zastosuj do wzorca elementu webhook poprzednią piekarni, a Wyobraź sobie, że wywołasz piekarni i porządkuje się niestandardową ciasto do dostarczenia. Proces tworzenia ciastka trwa i nie chce czekać na telefon, gdy piekarni działa na ciastku. Piekarni potwierdzi zamówienie, ale tym razem podajesz im numer telefonu, aby mogli wywoływać Cię po zakończeniu ciastka. Tym razem piekarni informuje o tym, kiedy zamówienie jest gotowe i dostarcza Twoje ciastko.

Gdy mapujemy ten wzorzec elementu webhook z powrotem, piekarni reprezentuje niestandardowy interfejs API, podczas gdy ty przejdziesz do niego aparat Logic Apps. Aparat wywołuje interfejs API z żądaniem i zawiera adres URL "wywołania zwrotnego".
Gdy zadanie zostanie wykonane, interfejs API użyje adresu URL, aby powiadomić aparat i zwrócić dane do aplikacji logiki, która następnie kontynuuje przepływ pracy. 

Dla tego wzorca Skonfiguruj dwa punkty końcowe na kontrolerze: `subscribe` i `unsubscribe`

*  `subscribe` punkt końcowy: gdy wykonanie osiągnie akcję interfejsu API w przepływie pracy, aparat Logic Apps wywołuje `subscribe` punkt końcowy. Ten krok powoduje, że aplikacja logiki tworzy adres URL wywołania zwrotnego, który przechowuje interfejs API, a następnie czeka na wywołanie zwrotne z interfejsu API po zakończeniu pracy. Interfejs API następnie wywołuje przy użyciu protokołu HTTP POST w adresie URL i przekazuje wszelkie zwrócone treści i nagłówki jako dane wejściowe do aplikacji logiki.

* `unsubscribe` punkt końcowy: Jeśli uruchomienie aplikacji logiki zostało anulowane, aparat Logic Apps wywoła `unsubscribe` punkt końcowy. Interfejs API może następnie wyrejestrować adres URL wywołania zwrotnego i zatrzymać procesy w razie potrzeby.

![Wzorzec akcji elementu webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

Obecnie projektant aplikacji logiki nie obsługuje odnajdywania punktów końcowych elementu webhook za pomocą struktury Swagger. Dlatego dla tego wzorca należy dodać [akcję **elementu webhook**](../connectors/connectors-native-webhook.md) i określić adres URL, nagłówki i treść żądania. Zobacz także [Akcje przepływu pracy i wyzwalacze](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Przykładowy wzorzec elementu webhook zapoznaj się z tym przykładem [wyzwalacza elementu webhook w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Oto kilka innych porad i uwag:

* Aby przekazać adres URL wywołania zwrotnego, można użyć `@listCallbackUrl()` funkcji przepływu pracy w dowolnym z poprzednich pól, zgodnie z potrzebami.

* Jeśli jesteś zarówno aplikacją logiki, jak i usługą subskrybowaną, nie musisz wywoływać `unsubscribe` punktu końcowego po wywołaniu adresu URL wywołania zwrotnego. W przeciwnym razie środowisko uruchomieniowe Logic Apps musi wywołać `unsubscribe` punkt końcowy, aby sygnalizować, że nie są oczekiwane kolejne wywołania i umożliwiają czyszczenie zasobów po stronie serwera.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Wzorce wyzwalacza

Niestandardowy interfejs API może działać jako [*wyzwalacz*](./logic-apps-overview.md#logic-app-concepts) , który uruchamia aplikację logiki, gdy nowe dane lub zdarzenie spełniają określony warunek. Ten wyzwalacz może sprawdzać regularnie lub czekać i nasłuchiwać nowych danych lub zdarzeń w punkcie końcowym usługi. Jeśli nowe dane lub zdarzenie spełniają określony warunek, wyzwalacz uruchamia i uruchamia aplikację logiki, która nasłuchuje tego wyzwalacza. Aby uruchamiać aplikacje logiki w ten sposób, interfejs API może postępować zgodnie z [*wyzwalaczem sondowania*](#polling-triggers) lub wzorcem [*wyzwalacza elementu webhook*](#webhook-triggers) . Wzorce te są podobne do ich odpowiedników dla [akcji sondowania](#async-pattern) i [akcji elementu webhook](#webhook-actions). Dowiedz się więcej o [zliczaniu użycia dla wyzwalaczy](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Regularnie sprawdzaj dostępność nowych danych lub zdarzeń za pomocą wzorca wyzwalacza sondowania

*Wyzwalacz sondowania* działa podobnie jak [Akcja sondowania](#async-pattern) opisana wcześniej w tym temacie. Aparat Logic Apps okresowo wywołuje i sprawdza punkt końcowy wyzwalacza dla nowych danych lub zdarzeń. Jeśli aparat odnajdzie nowe dane lub zdarzenie spełniające określony warunek, wyzwalany jest wyzwalacz. Następnie aparat tworzy wystąpienie aplikacji logiki, które przetwarza dane jako dane wejściowe. 

![Wzorzec wyzwalacza sondowania](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Każde żądanie sondowania jest traktowane jako wykonanie akcji nawet wtedy, gdy nie jest tworzone żadne wystąpienie aplikacji logiki. Aby zapobiec wielokrotnemu przetwarzaniu tych samych danych, wyzwalacz powinien czyścić dane, które zostały już odczytane i przesłane do aplikacji logiki.

Poniżej przedstawiono konkretne kroki dla wyzwalacza sondowania, które opisano w perspektywie interfejsu API:

| Znaleziono nowe dane lub wydarzenie?  | Odpowiedź interfejsu API | 
| ------------------------- | ------------ |
| Znaleziono | Zwróć stan HTTP `200 OK` z ładunkiem odpowiedzi (dane wejściowe dla następnego kroku). <br/>Ta odpowiedź tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy. | 
| Nie znaleziono | Zwróć stan HTTP `202 ACCEPTED` z `location` nagłówkiem i `retry-after` nagłówkiem. <br/>W przypadku wyzwalaczy `location` nagłówek powinien również zawierać `triggerState` parametr zapytania, który zwykle jest "sygnaturą czasową". Interfejs API może użyć tego identyfikatora do śledzenia czasu ostatniego uruchomienia aplikacji logiki. | 
||| 

Na przykład aby okresowo sprawdzać usługę pod kątem nowych plików, można utworzyć wyzwalacz sondowania, który ma następujące zachowania:

| Czy żądanie zawiera `triggerState` ? | Odpowiedź interfejsu API | 
| -------------------------------- | -------------| 
| Nie | Zwróć stan HTTP `202 ACCEPTED` `location` i nagłówek z `triggerState` ustawionym na bieżącą godzinę, a `retry-after` interwał na 15 sekund. | 
| Tak | Sprawdź, czy usługa zawiera pliki dodane po stronie `DateTime` for `triggerState` . | 
||| 

| Liczba znalezionych plików | Odpowiedź interfejsu API | 
| --------------------- | -------------| 
| Pojedynczy plik | Zwróć stan HTTP `200 OK` i ładunek zawartości, zaktualizuj `triggerState` do `DateTime` dla zwróconego pliku i ustaw `retry-after` interwał na 15 sekund. | 
| Wiele plików | Zwróć jeden plik jednocześnie i `200 OK` stan http, zaktualizuj `triggerState` i ustaw `retry-after` interwał na 0 sekund. </br>Te kroki umożliwiają aparatowi określenie, że dostępne są więcej danych i że aparat powinien natychmiast zażądać danych z adresu URL w `location` nagłówku. | 
| Brak plików | Zwróć stan HTTP `202 ACCEPTED` , nie zmieniaj `triggerState` i ustaw `retry-after` interwał na 15 sekund. | 
||| 

> [!TIP]
> Aby zapoznać się z przykładowym wzorcem wyzwalacza sondowania, przejrzyj ten [przykładowy kontroler wyzwalacza sondowania w witrynie GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Czekaj i Nasłuchuj nowych danych lub zdarzeń za pomocą wzorca wyzwalacza elementu webhook

Wyzwalacz elementu webhook to *wyzwalacz wypychania* , który czeka i nasłuchuje nowych danych lub zdarzeń w punkcie końcowym usługi. Jeśli nowe dane lub zdarzenie spełniają określony warunek, wyzwalacz uruchamia i tworzy wystąpienie aplikacji logiki, które następnie przetwarza dane jako dane wejściowe.
Wyzwalacze elementu webhook działają podobnie jak [Akcje elementu webhook](#webhook-actions) opisane wcześniej w tym temacie i są ustawiane za pomocą `subscribe` `unsubscribe` punktów końcowych i. 

* `subscribe` punkt końcowy: po dodaniu i zapisaniu wyzwalacza elementu webhook w aplikacji logiki aparat Logic Apps wywołuje `subscribe` punkt końcowy. Ten krok powoduje, że aplikacja logiki tworzy adres URL wywołania zwrotnego, który jest przechowywany w interfejsie API. Gdy pojawią się nowe dane lub zdarzenie spełniające określony warunek, interfejs API wywołuje zwrotnie przy użyciu protokołu HTTP POST na adres URL. Ładunek zawartości i nagłówki są przekazywane jako dane wejściowe do aplikacji logiki.

* `unsubscribe` punkt końcowy: Jeśli zostanie usunięty wyzwalacz elementu webhook lub cała aplikacja logiki, aparat Logic Apps wywoła `unsubscribe` punkt końcowy. Interfejs API może następnie wyrejestrować adres URL wywołania zwrotnego i zatrzymać procesy w razie potrzeby.

![Wzorzec wyzwalacza elementu webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

Obecnie projektant aplikacji logiki nie obsługuje odnajdywania punktów końcowych elementu webhook za pomocą struktury Swagger. Dlatego dla tego wzorca należy dodać [wyzwalacz **elementu webhook**](../connectors/connectors-native-webhook.md) i określić adres URL, nagłówki i treść żądania. Zobacz również [wyzwalacz HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Przykładowy wzorzec elementu webhook zapoznaj się z tym przykładem [kontrolera wyzwalacza elementu webhook w usłudze GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Oto kilka innych porad i uwag:

* Aby przekazać adres URL wywołania zwrotnego, można użyć `@listCallbackUrl()` funkcji przepływu pracy w dowolnym z poprzednich pól, zgodnie z potrzebami.

* Aby zapobiec wielokrotnemu przetwarzaniu tych samych danych, wyzwalacz powinien czyścić dane, które zostały już odczytane i przesłane do aplikacji logiki.

* Jeśli jesteś zarówno aplikacją logiki, jak i usługą subskrybowaną, nie musisz wywoływać `unsubscribe` punktu końcowego po wywołaniu adresu URL wywołania zwrotnego. W przeciwnym razie środowisko uruchomieniowe Logic Apps musi wywołać `unsubscribe` punkt końcowy, aby sygnalizować, że nie są oczekiwane kolejne wywołania i umożliwiają czyszczenie zasobów po stronie serwera.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Zwiększ bezpieczeństwo wywołań interfejsów API z usługi Logic Apps

Po utworzeniu niestandardowych interfejsów API Skonfiguruj uwierzytelnianie dla swoich interfejsów API, aby można było je bezpiecznie wywoływać z aplikacji logiki. Dowiedz się [, jak zwiększyć bezpieczeństwo wywołań niestandardowych interfejsów API z usługi Logic Apps](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Wdrażanie i wywoływanie interfejsów API

Po skonfigurowaniu uwierzytelniania należy skonfigurować wdrożenie dla interfejsów API. Dowiedz się [, jak wdrażać i wywoływać niestandardowe interfejsy API z usługi Logic Apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikowanie niestandardowych interfejsów API na platformie Azure

Aby udostępnić niestandardowe interfejsy API innym użytkownikom Logic Apps na platformie Azure, należy dodać zabezpieczenia i zarejestrować je jako łączniki aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Omówienie łączników niestandardowych](../logic-apps/custom-connector-overview.md). 

Aby udostępnić niestandardowe interfejsy API wszystkim użytkownikom w Logic Apps, w usłudze Automatyzacja i aplikacjach Microsoft PowerShell, musisz dodać zabezpieczenia, zarejestrować interfejsy API jako łączniki aplikacji logiki i wyznaczyć łączniki dla [programu certyfikowanego Microsoft Azure](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Aby uzyskać pomoc dotyczącą niestandardowych interfejsów API, skontaktuj się z pomocą techniczną [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) .

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).

* Aby pomóc w ulepszaniu usługi Logic Apps, przesyłaj pomysły lub głosuj na nie w [witrynie opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Następne kroki

* [Obsługa błędów i wyjątków](../logic-apps/logic-apps-exception-handling.md)
* [Wywoływanie, wyzwalanie lub zagnieżdżanie aplikacji logiki za pomocą punktów końcowych HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Pomiar użycia dla akcji i wyzwalaczy](../logic-apps/logic-apps-pricing.md)
