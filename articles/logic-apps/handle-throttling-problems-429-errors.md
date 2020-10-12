---
title: Rozwiązywanie problemów z ograniczeniami lub "429-zbyt wiele żądań"
description: Jak obejść problemy związane z ograniczaniem przepustowości lub "HTTP 429 zbyt wiele żądań" w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 495847d31682aff64fed3c81b1d5d68cf67dfd38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086443"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Obsłuż problemy związane z ograniczaniem (429-"zbyt wiele żądań") w Azure Logic Apps

W [Azure Logic Apps](../logic-apps/logic-apps-overview.md)aplikacja logiki zwraca [błąd "http 429 zbyt wiele żądań"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) , gdy występuje ograniczenie przepustowości, co się dzieje, gdy liczba żądań przekroczy szybkość, z jaką miejsce docelowe może obsłużyć w określonym czasie. Ograniczanie wydajności może spowodować problemy, takie jak opóźnione przetwarzanie danych, zmniejszona szybkość działania oraz błędy, takie jak przekroczenie określonych zasad ponawiania.

![Ograniczanie w łączniku SQL Server](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Poniżej przedstawiono niektóre typowe typy ograniczania przepustowości, które mogą wystąpić w aplikacji logiki:

* [Aplikacja logiki](#logic-app-throttling)
* [Łącznik](#connector-throttling)
* [Docelowa usługa lub system](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Ograniczanie aplikacji logiki

Usługa Azure Logic Apps ma własne [limity przepływności](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). W związku z tym, jeśli aplikacja logiki przekroczy te limity, zasób aplikacji logiki zostanie ograniczony, a nie tylko dla określonego wystąpienia lub uruchomienia.

Aby znaleźć zdarzenia ograniczania przepustowości na tym poziomie, Sprawdź okienko **metryki** aplikacji logiki w Azure Portal.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **monitorowanie**wybierz pozycję **metryki**.

1. W obszarze **tytuł wykresu**wybierz pozycję **Dodaj metrykę** , aby dodać kolejną metrykę do istniejącej.

1. Na pierwszym pasku metryki z listy **metryk** wybierz pozycję **Akcja ograniczenia zdarzeń**. Na drugim pasku metryki z listy **metryk** wybierz pozycję **Wyzwalaj zdarzenia ograniczone**.

Aby obsłużyć ograniczenie przepustowości na tym poziomie, dostępne są następujące opcje:

* Ogranicz liczbę wystąpień aplikacji logiki, które mogą być uruchamiane w tym samym czasie.

  Domyślnie, jeśli warunek wyzwalacza aplikacji logiki jest spełniony więcej niż raz w tym samym czasie, wiele wystąpień wyzwalaczy dla aplikacji logiki przebiega współbieżnie lub *równolegle*. To zachowanie oznacza, że każde wystąpienie wyzwalacza wyzwalane przed ukończeniem poprzedniego wystąpienia przepływu pracy.

  Mimo że domyślna liczba wystąpień wyzwalaczy, które mogą być uruchamiane współbieżnie, jest [nieograniczona](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), można ograniczyć tę liczbę, [włączając ustawienie współbieżności wyzwalacza](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency), a jeśli to konieczne, wybierz limit inny niż wartość domyślna.

* Włącz tryb wysokiej przepustowości.

  Aplikacja logiki ma [domyślny limit liczby akcji, które można uruchomić w ciągu 5-minutowego interwału](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Aby zgłosić ten limit do maksymalnej liczby akcji, Włącz [tryb wysokiej przepływności](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) w aplikacji logiki.

* Wyłącz zachowanie podczas tworzenia partii tablic ("Podziel na") w wyzwalaczach.

  Jeśli wyzwalacz zwraca tablicę dla pozostałych akcji przepływu pracy do przetworzenia, podział wyzwalacza [ **w** ustawieniach](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) dzieli elementy tablicy i uruchamia wystąpienie przepływu pracy dla każdego elementu tablicy, co skutecznie wyzwala wiele współbieżnych uruchomień do [ **podziału na** limit](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Aby kontrolować ograniczanie, wyłącz zachowanie **podziału przy** użyciu aplikacji logiki, a następnie Zaplanuj całą tablicę za pomocą pojedynczego wywołania, zamiast obsłużyć pojedynczy element dla każdego wywołania.

* Akcje refaktoryzacji w mniejszych aplikacjach logiki.

  Jak wspomniano wcześniej, aplikacja logiki jest ograniczona do [domyślnej liczby akcji, które można uruchomić w ciągu 5 minut](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Chociaż można zwiększyć ten limit, włączając [tryb wysokiej przepustowości](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), można również rozważyć, czy chcesz podzielić działania aplikacji logiki na mniejsze Aplikacje logiki, tak aby liczba akcji, które są uruchamiane w każdej aplikacji logiki, pozostawała poniżej limitu. Dzięki temu można zmniejszyć obciążenie pojedynczego zasobu aplikacji logiki i rozpowszechnić obciążenie w wielu aplikacjach logiki. To rozwiązanie działa lepiej w przypadku akcji obsługujących duże zbiory danych lub uruchamiania wielu współbieżnie uruchomionych akcji, iteracji pętli lub akcji wewnątrz każdej iteracji pętli, która przekracza limit wykonywania akcji.

  Na przykład ta aplikacja logiki wykonuje wszystkie czynności w celu pobrania tabel z bazy danych SQL Server i pobiera wiersze z każdej tabeli. Pętla **for each** jednocześnie wykonuje iterację w każdej tabeli, tak aby Akcja **Pobierz wiersze** zwraca wiersze dla każdej tabeli. Na podstawie ilości danych w tych tabelach akcje te mogą przekroczyć limit wykonywania akcji.

  ![Refaktoryzacja aplikacji logiki "przed"](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Po refaktoryzacji aplikacja logiki jest teraz nadrzędną i podrzędną aplikacją logiki. Element nadrzędny pobiera tabele z SQL Server a następnie wywołuje podrzędną aplikację logiki dla każdej tabeli w celu pobrania wierszy:

  ![Tworzenie aplikacji logiki dla jednej akcji](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Oto podrzędna aplikacja logiki, która jest wywoływana przez nadrzędną aplikację logiki, aby uzyskać wiersze dla każdej tabeli:

  ![Utwórz kolejną aplikację logiki dla drugiej akcji](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Ograniczanie łącznika

Każdy Łącznik ma własne limity ograniczania przepustowości, które można znaleźć na stronie dokumentacji technicznej łącznika. Na przykład [łącznik Azure Service Bus](/connectors/servicebus/) ma limit ograniczania, który zezwala na 6 000 wywołań na minutę, podczas gdy łącznik SQL Server ma [limity ograniczania, które różnią się w zależności od typu operacji](/connectors/sql/).

Niektóre wyzwalacze i akcje, takie jak HTTP, mają ["zasady ponawiania"](../logic-apps/logic-apps-exception-handling.md#retry-policies) , które można dostosować na podstawie [limitów zasad ponawiania](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) w celu zaimplementowania obsługi wyjątków. Te zasady określają, czy i jak często wyzwalacz lub akcja ponawiają żądanie, gdy oryginalne żądanie zakończy się niepowodzeniem lub przekroczy limit czasu, a wynikiem jest odpowiedź 408, 429 lub 5xx. W związku z tym, gdy ograniczanie zostanie rozpoczęte i zwróci błąd 429, Logic Apps następuje zgodnie z zasadami ponawiania, jeśli są obsługiwane.

Aby dowiedzieć się, czy wyzwalacz lub akcja obsługuje zasady ponawiania, sprawdź ustawienia wyzwalacza lub akcji. Aby wyświetlić wyzwalacz lub ponowić próbę wykonania działania, przejdź do historii uruchamiania aplikacji logiki, wybierz przebieg, który chcesz przejrzeć, i rozwiń ten wyzwalacz lub akcję, aby wyświetlić szczegóły dotyczące wejść, danych wyjściowych i wszelkich ponownych prób, na przykład:

![Wyświetlanie historii uruchamiania akcji, ponownych prób, wejść i wyjść](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Mimo że historia ponowień zawiera informacje o błędach, może wystąpić problem z rozróżnieniem ograniczenia przepustowości łącznika i [ograniczenia lokalizacji docelowej](#destination-throttling). W takim przypadku może być konieczne przejrzenie szczegółów odpowiedzi lub przeprowadzenie obliczeń dotyczących interwału ograniczania w celu zidentyfikowania źródła.

W przypadku usługi Logic Apps w globalnej, wielodostępnej usłudze Azure Logic Apps, ograniczenie przepustowości odbywa się na poziomie *połączenia* . W związku z tym na przykład w przypadku aplikacji logiki, które działają w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), ograniczenie przepustowości nadal odbywa się dla połączeń innych niż ISE, ponieważ są one uruchamiane w globalnej, wielodostępnej Logic Apps usłudze. Jednak połączenia ISE, które są tworzone przez łączniki ISE, nie są ograniczane, ponieważ działają w ISE.

Aby obsłużyć ograniczenie przepustowości na tym poziomie, dostępne są następujące opcje:

* Skonfiguruj wiele połączeń dla jednej akcji, aby aplikacja logiki pogenerowała dane do przetworzenia.

  W przypadku tej opcji należy rozważyć, czy można rozpowszechnić obciążenie, dzieląc żądania akcji na wiele połączeń z tym samym miejscem docelowym przy użyciu tych samych poświadczeń.

  Załóżmy na przykład, że aplikacja logiki pobiera tabele z bazy danych SQL Server, a następnie pobiera wiersze z każdej tabeli. Na podstawie liczby wierszy, które trzeba przetworzyć, można użyć wielu połączeń i wielu **dla każdej** pętli, aby podzielić łączną liczbę wierszy na mniejsze zestawy do przetworzenia. W tym scenariuszu dwie **dla każdej** pętli są stosowane do dzielenia łącznej liczby wierszy w połowie. Pierwsze **dla każdej** pętli używa wyrażenia, które pobiera pierwszą połowę. Druga **dla każdej** pętli używa innego wyrażenia, które pobiera drugą połowę, na przykład:<p>

    * Wyrażenie 1: `take()` funkcja pobiera przód kolekcji. Aby uzyskać więcej informacji, zobacz [ **`take()`** funkcję](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Wyrażenie 2: `skip()` Funkcja usuwa przód kolekcji i zwraca wszystkie pozostałe elementy. Aby uzyskać więcej informacji, zobacz [ **`skip()`** funkcję](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Oto przykład wizualizacji, który pokazuje, jak można używać tych wyrażeń:

    ![Tworzenie i używanie wielu połączeń dla jednej akcji](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Skonfiguruj inne połączenie dla każdej akcji.

  W przypadku tej opcji należy rozważyć, czy można rozpowszechnić obciążenie, rozkładając żądania poszczególnych akcji w ramach własnego połączenia, nawet gdy akcje łączą się z tą samą usługą lub systemem i używają tych samych poświadczeń.

  Załóżmy na przykład, że aplikacja logiki pobiera tabele z bazy danych SQL Server i pobiera każdy wiersz w każdej tabeli. Możesz użyć oddzielnych połączeń, aby Pobieranie tabel używało jednego połączenia, podczas gdy pobiera każdy wiersz używa innego połączenia.

  ![Tworzenie i używanie innych połączeń dla każdej akcji](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Zmień współbieżność lub równoległość dla [pętli "for each"](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Domyślnie dla iteracji pętli "for each" są uruchamiane w tym samym czasie aż do [limitu współbieżności](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Jeśli masz łącznik, który jest ograniczony w pętli "for each", możesz zmniejszyć liczbę iteracji pętli, które są uruchamiane równolegle. Więcej informacji można znaleźć w następujących tematach:
  
  * ["Dla każdego" pętli — Zmień współbieżność lub Uruchom sekwencyjnie](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schemat języka definicji przepływu pracy — dla każdej pętli](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schemat języka definicji przepływu pracy — Zmień "dla każdej" współbieżności pętli](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schemat języka definicji przepływu pracy — uruchamiaj kolejne pętle "for each"](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Ograniczanie usługi lub systemu docelowego

Chociaż Łącznik ma własne limity ograniczania przepustowości, usługa docelowa lub system, który jest wywoływany przez łącznik, może również mieć limity ograniczania. Na przykład niektóre interfejsy API w programie Microsoft Exchange Server mają rygorystyczne limity ograniczania przepustowości niż łącznik usługi Office 365 Outlook.

Domyślnie wystąpienia aplikacji logiki i wszelkie pętle lub gałęzie wewnątrz tych wystąpień są uruchamiane *równolegle*. To zachowanie oznacza, że wiele wystąpień może wywoływać ten sam punkt końcowy w tym samym czasie. Każde wystąpienie nie wie o istnieniu drugiego, dlatego prób ponowienia akcji może stworzyć sytuacje [wyścigu](https://en.wikipedia.org/wiki/Race_condition) , w których wiele wywołań próbuje się uruchomić w tym samym czasie, ale pomyślne, te wywołania muszą dotrzeć do docelowej usługi lub systemu przed rozpoczęciem ograniczania.

Załóżmy na przykład, że masz tablicę zawierającą 100 elementów. Za pomocą pętli "for each" można wykonać iterację tablicy i włączyć kontrolę współbieżności pętli, aby można było ograniczyć liczbę równoległych iteracji do 20 lub [bieżącego domyślnego limitu](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Wewnątrz tej pętli akcja wstawia element z tablicy do bazy danych SQL Server, co umożliwia tylko 15 wywołań na sekundę. W tym scenariuszu wystąpił problem z ograniczeniami, ponieważ zaległe próby skompilowania i nigdy nie zostaną uruchomione.

W tej tabeli opisano oś czasu, dla której występuje w pętli, gdy interwał ponawiania prób działania wynosi 1 sekunda:

| Punkt w czasie | Liczba uruchomionych akcji | Liczba akcji, które kończą się niepowodzeniem | Liczba ponownych prób oczekujących |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 s | 20 wstawek | 5 nie powiodło się ze względu na limit SQL | 5 ponownych prób |
| T + 0,5 sekund | 15 wstawek, z powodu wcześniejszej 5 ponownych prób | 15 zakończonych niepowodzeniem, z powodu powyższego limitu SQL nadal obowiązuje przez inne 0,5 sekund | 20 ponownych prób <br>(poprzedni 5 + 15 nowość) |
| T + 1 sekunda | 20 wstawek | 5 nieudane Plus 20 ponownych prób, ze względu na limit SQL | 25 ponownych prób (poprzedni 20 + 5 nowość)
|||||

Aby obsłużyć ograniczenie przepustowości na tym poziomie, dostępne są następujące opcje:

* Twórz aplikacje logiki, aby każdy obsługiwał jedną operację.

  * Kontynuując pracę z przykładowym scenariuszem SQL Server w tej sekcji, można utworzyć aplikację logiki, która umieszcza elementy tablicowe w kolejce, takie jak [kolejka Azure Service Bus](../connectors/connectors-create-api-servicebus.md). Następnie utworzysz kolejną aplikację logiki, która wykonuje tylko operację wstawiania dla każdego elementu w tej kolejce. W ten sposób tylko jedno wystąpienie aplikacji logiki jest uruchamiane w dowolnym określonym czasie, który wykonuje operację wstawiania i przechodzi do następnego elementu w kolejce, a wystąpienie otrzymuje błędy 429, ale nie próbuje wykonać ponownej próby.

  * Utwórz nadrzędną aplikację logiki, która wywołuje podrzędną lub zagnieżdżoną aplikację logiki dla każdej akcji. Jeśli element nadrzędny musi wywoływać różne aplikacje podrzędne na podstawie wyniku elementu nadrzędnego, można użyć akcji warunkowej lub przełącznika, która określa, która aplikacja podrzędna ma być wywoływana. Ten wzorzec może pomóc w zmniejszeniu liczby wywołań lub operacji.

    Załóżmy na przykład, że masz dwie aplikacje logiki, z których każdy ma wyzwalacz sondowania, który sprawdza konto e-mail co minutę w celu uzyskania określonego tematu, takiego jak "powodzenie" lub "Niepowodzenie". To ustawienie powoduje 120 wywołań na godzinę. Zamiast tego, jeśli utworzysz pojedynczą nadrzędną aplikację logiki, która sonduje co minutę, ale wywołuje podrzędną aplikację logiki, która jest uruchamiana w zależności od tego, czy podmiot ma wartość "powodzenie" lub "Niepowodzenie", zmniejszasz liczbę testów sondowania do połowy lub 60 w tym przypadku.

* Skonfiguruj przetwarzanie wsadowe.

  Jeśli usługa docelowa obsługuje operacje wsadowe, można rozwiązywać ograniczenia przez przetwarzanie elementów w grupach lub partiach, a nie osobno. Aby zaimplementować rozwiązanie przetwarzania wsadowego, należy utworzyć aplikację logiki "odbiorca wsadowa" i aplikację logiki "nadawca partii". Nadawca wsadowy zbiera komunikaty lub elementy do czasu spełnienia określonych kryteriów, a następnie wysyła te komunikaty lub elementy z pojedynczej grupy. Odbiorca wsadu akceptuje tę grupę i przetwarza te komunikaty lub elementy. Aby uzyskać więcej informacji, zobacz [Batch Process messages in Groups](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Użyj wersji elementu webhook dla wyzwalaczy i akcji, a nie wersji sondowania.

  Dlaczego? Wyzwalacz sondowania kontynuuje sprawdzanie docelowej usługi lub systemu w określonych odstępach czasu. Bardzo częste interwały, na przykład co sekundę, mogą tworzyć problemy z ograniczeniami. Jednak wyzwalacz elementu webhook lub Akcja, taka jak [element webhook protokołu HTTP](../connectors/connectors-native-webhook.md), tworzy tylko jedno wywołanie do docelowej usługi lub systemu, które odbywa się w czasie subskrypcji i żądania, że miejsce docelowe powiadamia wyzwalacz lub akcję tylko wtedy, gdy wystąpi zdarzenie. Dzięki temu wyzwalacz lub akcja nie musi stale sprawdzać lokalizacji docelowej.
  
  Dlatego jeśli usługa lub system docelowy obsługuje elementy webhook lub udostępnia łącznik, który ma wersję elementu webhook, ta opcja jest lepsza niż przy użyciu wersji sondowania. Aby zidentyfikować wyzwalacze i akcje elementu webhook, upewnij się, że mają one `ApiConnectionWebhook` Typ lub że nie wymagają określenia cyklu. Aby uzyskać więcej informacji, zobacz [APIConnectionWebhook wyzwalacz](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) i [APIConnectionWebhook akcji](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [limitów Logic Apps i konfiguracji](../logic-apps/logic-apps-limits-and-config.md)
