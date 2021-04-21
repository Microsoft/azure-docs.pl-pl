---
title: Wbudowane wyzwalacze i akcje dla Azure Logic Apps
description: Za pomocą wbudowanych wyzwalaczy i akcji można tworzyć zautomatyzowane przepływy pracy, które integrują aplikacje, dane, usługi i systemy, kontrolują przepływy pracy i zarządzają danymi przy użyciu Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796929"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Wbudowane wyzwalacze i akcje dla Logic Apps


[Wbudowane wyzwalacze i](apis-list.md) akcje zapewniają sposoby kontrolowania harmonogramu i struktury przepływu [pracy,](#control-workflow)uruchamiania własnego [kodu,](#run-code-from-workflows)zarządzania danymi i manipulowania nimi oraz wykonywania innych zadań w przepływach pracy. [](#manage-or-manipulate-data) Różni się [od łączników](managed.md)zarządzanych — wiele wbudowanych operacji nie jest powiązanych z określoną usługą, systemem lub protokołem. Na przykład możesz uruchomić niemal każdy przepływ pracy zgodnie z harmonogramem przy użyciu wyzwalacza Cykl. Możesz też poczekać na uruchomienie przepływu pracy przy użyciu wyzwalacza Żądania. Wszystkie wbudowane operacje są uruchamiane natywnie w usłudze Logic Apps, a większość z nich nie wymaga utworzenia połączenia przed ich użyciem. 

Logic Apps udostępnia również wbudowane operacje dla mniejszej liczby usług, systemów i protokołów, takich jak Azure Service Bus, Azure Functions, SQL, AS2 i tak dalej. Liczba i zakres różnią się w zależności od tego, czy tworzysz aplikację logiki z wieloma dzierżawami, czy aplikację logiki z jedną dzierżawą. W kilku przypadkach dostępna jest zarówno wersja wbudowana, jak i wersja łącznika zarządzanego. W większości przypadków wbudowana wersja zapewnia lepszą wydajność, możliwości, ceny i tak dalej. Aby na przykład wymieniać komunikaty B2B przy użyciu protokołu [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md)wybierz wbudowaną wersję, chyba że potrzebujesz możliwości śledzenia, które są dostępne tylko w (przestarzałej) wersji łącznika zarządzanego.

Na poniższej liście opisano tylko niektóre zadania, które można wykonać za pomocą wbudowanych [wyzwalaczy i akcji](#understand-triggers-and-actions):

- Uruchamianie przepływów pracy przy użyciu harmonogramów niestandardowych i zaawansowanych. Aby uzyskać więcej informacji na temat planowania, zapoznaj się z sekcją zachowania cyklu w [te Logic Apps omówienie łącznika.](apis-list.md#recurrence-behavior)
- Organizowanie i kontrolowanie struktury przepływu pracy, na przykład przy użyciu pętli i warunków.
- Praca ze zmiennymi, datami, operacjami na danych, przekształceniami zawartości i operacjami wsadowym.
- Komunikacja z innymi punktami końcowymi przy użyciu wyzwalaczy i akcji HTTP.
- Odbieranie żądań i odpowiadanie na nie.
- Wywołaj własne funkcje (Azure Functions), aplikacje internetowe (Azure App Services), interfejsy API (Azure API Management), inne przepływy pracy usługi Logic Apps, które mogą odbierać żądania i tak dalej.

## <a name="understand-triggers-and-actions"></a>Opis wyzwalaczy i akcji

Logic Apps udostępnia następujące wbudowane wyzwalacze i akcje:

:::row:::
    :::column:::
        [![Ikona Harmonogramu w Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**Harmonogram**][schedule-doc]
        \
        \
        [**Cykl:**][schedule-recurrence-doc]wyzwalanie przepływu pracy na podstawie określonego cyklu.
        \
        \
        [**Okno przesuwania:**][schedule-sliding-window-doc]wyzwala przepływ pracy, który musi obsługiwać dane w ciągłych fragmentach.
        \
        \
        [**Opóźnienie:**][schedule-delay-doc]Wstrzymaj przepływ pracy na określony czas.
        \
        \
        [**Opóźnienie do**][schedule-delay-until-doc]: Wstrzymaj przepływ pracy do określonej daty i godziny.
    :::column-end:::
    :::column:::
        [![Ikona usługi Batch w Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Partii**][batch-doc]
        \
        \
        [**Komunikaty**][batch-doc]wsadowe: wyzwalanie przepływu pracy, który przetwarza komunikaty w partiach.
        \
        \
        [**Wysyłanie komunikatów do partii:**][batch-doc]wywołaj istniejący przepływ pracy, który obecnie rozpoczyna się od **wyzwalacza komunikatów usługi Batch.**
    :::column-end:::
    :::column:::
        [![Ikona HTTP w Logic Apps][http-icon]][http-doc]
        \
        \
        [**Protokół HTTP**][http-doc]
        \
        \
        Wywołaj punkt końcowy HTTP lub HTTPS przy użyciu wyzwalacza lub akcji HTTP. 
        \
        \
        Możesz również użyć tych innych wbudowanych wyzwalaczy i akcji HTTP: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP i webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Ikona żądania][http-request-icon]][http-request-doc]
        \
        \
        [**Żądanie**][http-request-doc]
        \
        \
        [**Po otrzymaniu żądania HTTP:**][http-request-doc]poczekaj na żądanie z innego przepływu pracy, aplikacji lub usługi. Ten wyzwalacz sprawia, że przepływ pracy jest wywoływany bez konieczności sprawdzenia lub sondowania zgodnie z harmonogramem.
        \
        \
        [**Odpowiedź:**][http-request-doc]odpowiadanie na żądanie odebrane przez wyzwalacz **Po otrzymaniu żądania HTTP** w tym samym przepływie pracy.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona API Management azure Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Wywoływanie własnych wyzwalaczy i akcji w interfejsach API, które definiujesz, publikujesz i zarządzasz nimi za pomocą [usługi Azure API Management.](../api-management/api-management-key-concepts.md) <p><p>**Uwaga:** nie jest obsługiwana w przypadku korzystania [z warstwy Zużycie dla API Management](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Ikona App Services azure Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Wywołaj aplikacje, które tworzysz i hostuj na [Azure App Service](../app-service/overview.md), na przykład API Apps i Web Apps.
        \
        \
        Gdy program Swagger jest dołączony, wyzwalacze i akcje zdefiniowane przez te aplikacje są wyświetlane jak wszystkie inne wyzwalacze i akcje pierwszej klasy w Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Azure Logic Apps ikona Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Wywołaj inne przepływy pracy, które rozpoczynają się od wyzwalacza Żądania o nazwie **Po otrzymaniu żądania HTTP.**
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Uruchamianie kodu z przepływów pracy

Logic Apps udostępnia wbudowane akcje do uruchamiania własnego kodu w przepływie pracy:

:::row:::
    :::column:::
        [![Azure Functions ikona Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        Wywołaj [funkcje hostowane na](../azure-functions/functions-overview.md) platformie Azure, aby uruchamiać własne fragmenty kodu (C# lub Node.js) w ramach przepływu pracy. 
    :::column-end:::
    :::column:::
        [![Ikona kodu wbudowanego w Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Kod w tekście**][inline-code-doc]
        \
        \
        [**Wykonaj kod JavaScript:**][inline-code-doc]Dodaj i uruchom własne fragmenty kodu *JavaScript* w ramach przepływu pracy.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Sterowanie przepływem pracy

Logic Apps udostępnia wbudowane akcje do struktury i kontrolowania akcji w przepływie pracy:

:::row:::
    :::column:::
        [![Ikona akcji warunek w Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Warunek**][condition-doc]
        \
        \
        Oceń warunek i uruchom różne akcje w zależności od tego, czy warunek ma wartość true, czy false.
    :::column-end:::
    :::column:::
        [![Dla każdej ikony akcji w Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        Wykonaj te same akcje dla każdego elementu w tablicy.
    :::column-end:::
    :::column:::
        [![Ikona akcji zakresu w Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**Nazwa**][scope-doc]
        \
        \
        Grupuj *akcje w zakresy*, które mają własny stan po zakończeniu działania akcji w zakresie.
    :::column-end:::
    :::column:::
        [![Ikona przełączania akcji w Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Przełącznik**][switch-doc]
        \
        \
        Pogrupuj *akcje w* przypadki , do których są przypisane unikatowe wartości z wyjątkiem przypadku domyślnego. Uruchom tylko ten przypadek, którego przypisana wartość pasuje do wyniku wyrażenia, obiektu lub tokenu. Jeśli żadne dopasowania nie istnieją, uruchom przypadek domyślny.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona akcji Zakończ w Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Zakończyć**][terminate-doc]
        \
        \
        Zatrzymaj aktywnie uruchomiony przepływ pracy aplikacji logiki. 
    :::column-end:::
    :::column:::
        [![Ikona akcji do momentu Logic Apps][until-icon]][until-doc]
        \
        \
        [**Do**][until-doc]
        \
        \
        Powtarzaj akcje do momentu, aż określony warunek będzie prawdziwy lub jakiś stan się zmieni.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Zarządzanie danymi lub manipulowanie nimi

Logic Apps udostępnia wbudowane akcje do pracy z danymi wyjściowych i ich formatami:

:::row:::
    :::column:::
        [![Ikona akcji Operacje na danych w Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Operacje na danych**][data-operations-doc]
        \
        \
        Wykonywanie operacji na danych. 
        \
        \
        **Utwórz:** utwórz pojedyncze dane wyjściowe z wielu danych wejściowych o różnych typach. 
        \
        \
        **Tworzenie tabeli CSV:** utwórz tabelę wartości rozdzielanych przecinkami (CSV) z tablicy z obiektami JSON. 
        \
        \
        **Tworzenie tabeli HTML:** utwórz tabelę HTML z tablicy z obiektami JSON. 
        \
        \
        **Filtruj** tablicę: utwórz tablicę na podstawie elementów w innej tablicy, które spełniają twoje kryteria. 
        \
        \
        **Sprzężenia:** Utwórz ciąg ze wszystkich elementów w tablicy i oddziel te elementy za pomocą określonego ogranicznika. 
        \
        \
        **Analizuj dane JSON:** twórz przyjazne dla użytkownika tokeny z właściwości i ich wartości w zawartości JSON, aby można było używać tych właściwości w przepływie pracy. 
        \
        \
        **Wybierz** pozycję : Utwórz tablicę z obiektami JSON, przekształcając elementy lub wartości w innej tablicy i mapując te elementy na określone właściwości.
    :::column-end:::
    :::column:::
        ![Ikona akcji Data i godzina w Logic Apps][date-time-icon]
        \
        \
        **Data i godzina**
        \
        \
        Wykonywanie operacji ze znacznikami czasu.
        \
        \
        **Dodaj do czasu:** Dodaj określoną liczbę jednostek do znacznika czasu. 
        \
        \
        **Konwertowanie strefy czasowej:** przekonwertuj znacznik czasu ze źródłowej strefy czasowej na docelową strefę czasową. 
        \
        \
        **Bieżąca godzina:** zwraca bieżący znacznik czasu w postaci ciągu. 
        \
        \
        **Pobierz przyszły czas:** zwróć bieżący znacznik czasu plus określone jednostki czasu. 
        \
        \
        **Pobierz czas przeszły:** zwróć bieżący znacznik czasu minus określone jednostki czasu. 
        \
        \
        **Odejmij** od czasu: odejmij liczbę jednostek czasu od znacznika czasu.
    :::column-end:::
    :::column:::
        [![Ikona akcji zmiennych w Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Zmiennych**][variables-doc]
        \
        \
        Wykonywanie operacji za pomocą zmiennych.
        \
        \
        **Dołącz do zmiennej tablicowej:** Wstaw wartość jako ostatni element w tablicy przechowywanej przez zmienną. 
        \
        \
        **Dołącz do zmiennej ciągu:** Wstaw wartość jako ostatni znak w ciągu przechowywanym przez zmienną.
        \
        \
        **Zmienna dekrementcyjna:** zmniejsz zmienną o wartość stałą.
        \
        \
        **Zmienna** inkrementacja: zwiększ zmienną o wartość stałą. 
        \
        \
        **Inicjowanie zmiennej:** utwórz zmienną i zadeklaruj jej typ danych oraz wartość początkową. 
        \
        \
        **Ustaw zmienną:** przypisz inną wartość do istniejącej zmiennej. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych interfejsów API, które można wywołać z Logic Apps](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Tworzenie wystąpienia usługi Azure API Management service do zarządzania interfejsami API i publikowania ich"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integracja aplikacji logiki z usługą App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrowanie aplikacji logiki z Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Przetwarzanie komunikatów w grupach lub jako partie"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Oceń warunek i uruchom różne akcje w zależności od tego, czy warunek ma wartość true czy false"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Wykonywanie tych samych akcji dla każdego elementu w tablicy"
[http-doc]: ./connectors-native-http.md "Wywołanie punktów końcowych HTTP lub HTTPS z aplikacji logiki"
[http-request-doc]: ./connectors-native-reqres.md "Odbieranie żądań HTTP w aplikacjach logiki"
[http-response-doc]: ./connectors-native-reqres.md "Odpowiadanie na żądania HTTP z aplikacji logiki"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Wywołanie punktów końcowych REST z aplikacji logiki"
[http-webhook-doc]: ./connectors-native-webhook.md "Oczekiwanie na określone zdarzenia z punktów końcowych HTTP lub HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Dodawanie i uruchamianie fragmentów kodu JavaScript z aplikacji logiki"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integracja aplikacji logiki z zagnieżdżonymi przepływami pracy"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Wybieranie i filtrowanie tablic za pomocą akcji zapytania"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Uruchamianie aplikacji logiki na podstawie harmonogramu"
[schedule-delay-doc]: ./connectors-native-delay.md "Opóźnienie uruchamiania następnej akcji"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Opóźnienie uruchamiania następnej akcji"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Uruchamianie aplikacji logiki zgodnie z harmonogramem cyklicznym"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Uruchamianie aplikacji logiki, które muszą obsługiwać dane w ciągłych fragmentach"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizowanie akcji w grupy, które mają własny stan po zakończeniu działania akcji w grupie"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizuj akcje w przypadkach, do których są przypisywane unikatowe wartości. Uruchom tylko przypadek, którego wartość pasuje do wyniku wyrażenia, obiektu lub tokenu. Jeśli żadne dopasowania nie istnieją, uruchom przypadek domyślny"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Zatrzymywanie lub anulowanie aktywnie działającego przepływu pracy dla aplikacji logiki"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Powtarzaj akcje do momentu, aż określony warunek będzie prawdziwy lub jakiś stan się zmieni"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Wykonywanie operacji na danych, takich jak filtrowanie tablic lub tworzenie tabel CSV i HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Wykonywanie operacji za pomocą zmiennych, takich jak inicjowanie, ustawianie, inkrementacja, dekrementacja i dołączanie do ciągu lub zmiennej tablicowej"
