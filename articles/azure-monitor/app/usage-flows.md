---
title: Usługa Azure Application Insights Przepływy użytkownika analizuje przepływy nawigacji
description: Analizuj, w jaki sposób użytkownicy przechodźją między stronami i funkcjami aplikacji sieci Web.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a31ab24b96fa44787d08801a0680f94ff98bb5a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309273"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Analizuj wzorce nawigacyjne użytkownika za pomocą Przepływy użytkownika w Application Insights

![Narzędzie Application Insights Przepływy użytkownika](./media/usage-flows/flows.png)

Narzędzie Przepływy użytkownika wizualizuje sposób nawigowania między stronami i funkcjami witryny. Doskonale nadaje się do odpowiedzi na pytania, takie jak:

* Jak użytkownicy nawigują poza stroną w witrynie?
* Co użytkownicy klikają stronę w witrynie?
* Gdzie są miejsca, w których użytkownicy przenoszą większość z lokacji?
* Czy istnieją miejsca, w których użytkownicy powtarzają tę samą akcję w trybie i powyżej?

Narzędzie Przepływy użytkownika zaczyna się od początkowego widoku strony, zdarzenia niestandardowego lub określonego wyjątku. Po podanym zdarzeniu początkowym Przepływy użytkownika pokazuje zdarzenia, które wystąpiły przed i później podczas sesji użytkownika. Linie o różnej grubości pokazują, ile razy następują poszczególne ścieżki od użytkowników. Węzły o **uruchomionej sesji** specjalnej pokazują, gdzie kolejne węzły rozpoczęły sesję. Węzły **zakończone sesją** pokazują, ilu użytkowników nie wysłały żadnych wyświetleń stron ani zdarzeń niestandardowych po poprzednim węźle, wyróżnianie, gdzie użytkownicy prawdopodobnie pozostawiły Twoją lokację.

> [!NOTE]
> Zasób Application Insights musi zawierać widoki stron lub zdarzenia niestandardowe, aby można było użyć narzędzia Przepływy użytkownika. [Dowiedz się, jak skonfigurować aplikację do automatycznego zbierania wyświetleń stron przy użyciu zestawu SDK języka JavaScript Application Insights](./javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Zacznij od wybrania początkowego zdarzenia

![Wybierz początkowe zdarzenie dla Przepływy użytkownika](./media/usage-flows/initial-event.png)

Aby zacząć odpowiadać na pytania za pomocą narzędzia Przepływy użytkownika, wybierz początkowy widok strony, zdarzenie niestandardowe lub wyjątek, który będzie używany jako punkt początkowy wizualizacji:

1. Kliknij link w tytule jak **Użytkownicy robią po...?** lub kliknij przycisk **Edytuj** .
2. Wybierz widok strony, zdarzenie niestandardowe lub wyjątek z początkowego menu rozwijanego **zdarzenia** .
3. Kliknij przycisk **Utwórz Graf**.

Kolumna "krok 1" wizualizacji pokazuje, co użytkownicy najczęściej tuż po zdarzeniu początkowym, uporządkowane od góry do dołu od najbardziej do najmniej częste. Elementy "krok 2" i kolejne kolumny pokazują, co Ci się stało, tworząc obraz wszystkich sposobów, w których użytkownicy przejdzie przez witrynę.

Domyślnie narzędzie Przepływy użytkownika losowo próbkuje tylko ostatnie 24 godziny wyświetlania stron i zdarzenia niestandardowego z witryny. Można zwiększyć zakres czasu i zmienić bilans wydajności i dokładność próbkowania losowego w menu Edycja.

Jeśli niektóre z wyświetleń stron, zdarzeń niestandardowych i wyjątków nie są odpowiednie dla użytkownika, kliknij przycisk **X** na węzłach, które chcesz ukryć. Po wybraniu węzłów do ukrycia kliknij przycisk **Utwórz wykres** poniżej wizualizacji. Aby wyświetlić wszystkie ukryte węzły, kliknij przycisk **Edytuj** , a następnie zapoznaj się z sekcją **wykluczone zdarzenia** .

Jeśli brakuje widoków stron lub zdarzeń niestandardowych, oczekiwane jest wyświetlanie wizualizacji:

* Sprawdź sekcję **wykluczone zdarzenia** w menu **Edycja** .
* Użyj przycisków Plus w **innych** węzłach, aby dołączyć mniej częste zdarzenia w wizualizacji.
* Jeśli widok strony lub zdarzenie niestandardowe, które oczekujesz, są wysyłane rzadko przez użytkowników, spróbuj zwiększyć zakres czasu wizualizacji w menu **Edycja** .
* Upewnij się, że widok strony, zdarzenie niestandardowe lub wyjątek są skonfigurowane do zebrania przez zestaw SDK Application Insights w kodzie źródłowym witryny. [Dowiedz się więcej na temat zbierania zdarzeń niestandardowych.](./api-custom-events-metrics.md)

Jeśli chcesz zobaczyć więcej kroków w wizualizacji, użyj **poprzednich kroków** i list rozwijanych **następnych kroków** nad wizualizacją.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Po odwiedzeniu strony lub funkcji, gdzie się znajdują Użytkownicy i co to zrobić?

![Użyj Przepływy użytkownika, aby zrozumieć, gdzie użytkownicy klikają](./media/usage-flows/one-step.png)

Jeśli wydarzenie początkowe jest widokiem strony, pierwsza kolumna ("krok 1") wizualizacji jest szybkim sposobem zrozumienia, co użytkownicy natychmiast po odwiedzeniu strony. Spróbuj otworzyć witrynę w oknie obok wizualizacji Przepływy użytkownika. Porównanie oczekiwań sposobu działania użytkowników ze stroną na listę zdarzeń w kolumnie "krok 1". Często element interfejsu użytkownika na stronie, który wydaje się nieistotny dla Twojego zespołu, może należeć do najczęściej używanego na stronie. Może to być świetny punkt wyjścia do udoskonalania projektu w witrynie.

Jeśli wydarzenie początkowe jest zdarzeniem niestandardowym, w pierwszej kolumnie są wyświetlane informacje o tym, co użytkownicy przeprowadzili po wykonaniu tej akcji. Podobnie jak w przypadku wyświetleń stron, należy rozważyć, czy obserwowane zachowanie użytkowników jest zgodne z celami i oczekiwaniami Twojego zespołu. Jeśli wybrane wydarzenie początkowe ma wartość "dodano element do koszyka", na przykład sprawdź, czy w wizualizacji pojawiło się wkrótce polecenie "przejdź do wyewidencjonowania" i "ukończono zakup". Jeśli zachowanie użytkownika różni się od oczekiwań, użyj wizualizacji, aby zrozumieć, jak użytkownicy są "zalewkowane" w bieżącym projekcie witryny.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Gdzie są miejsca, w których użytkownicy przenoszą większość z lokacji?

Obejrzyj węzły **zakończone sesjami** , które są wyświetlane wysoko w kolumnie w wizualizacji, szczególnie wczesne w przepływie. Oznacza to, że wiele użytkowników prawdopodobnie zostało zmienionych z witryny po powyższej ścieżce stron i interakcji z interfejsem użytkownika. Czasami zmiany są oczekiwane — po zakończeniu zakupu w witrynie handlu elektronicznego, na przykład zwykle jest to znak problemów projektowych, niska wydajność lub inne problemy z witryną, które można ulepszyć.

Należy pamiętać, że węzły **zakończone sesjami** są oparte tylko na danych telemetrycznych zebranych przez ten Application Insights zasób. Jeśli Application Insights nie odbiera danych telemetrycznych dla pewnych interakcji z użytkownikiem, użytkownicy mogli nadal korzystać z witryny w taki sposób, aby po zakończeniu działania narzędzia Przepływy użytkownika wyświetlona została sesja.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Czy istnieją miejsca, w których użytkownicy powtarzają tę samą akcję w trybie i powyżej?

Wyszukaj widok strony lub zdarzenie niestandardowe powtarzane przez wielu użytkowników w kolejnych krokach w wizualizacji. Zazwyczaj oznacza to, że użytkownicy wykonują powtarzające się działania w witrynie. Jeśli znajdziesz powtarzanie, pomyśl o zmianie projektu witryny lub dodaniu nowej funkcji, aby zmniejszyć powtarzanie. Na przykład dodanie funkcji Edytuj zbiorczo, jeśli znajdziesz użytkowników wykonujących powtarzające się czynności na każdym wierszu elementu tabeli.

## <a name="common-questions"></a>Często zadawane pytania

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>Czy początkowe zdarzenie reprezentuje pierwsze zdarzenie pojawiające się w sesji lub gdy pojawia się w sesji?

Początkowe zdarzenie w wizualizacji jest reprezentowane tylko podczas pierwszego wysłania przez użytkownika tego widoku strony lub zdarzenia niestandardowego podczas sesji. Jeśli użytkownicy mogą wielokrotnie wysyłać wydarzenie początkowe w sesji, w kolumnie "krok 1" pokazuje się, jak działają użytkownicy po *pierwszym* wystąpieniu zdarzenia początkowego, nie wszystkie wystąpienia.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Niektóre węzły w mojej wizualizacji są zbyt wysokie. Na przykład węzeł, który właśnie mówi "kliknięto przycisk". Jak mogę podzielić na bardziej szczegółowe węzły?

Użyj opcji **Podziel według** w menu **Edycja** :

1. Wybierz zdarzenie, które chcesz przerwać w menu **zdarzenie** .
2. Wybierz wymiar w menu **wymiar** . Na przykład jeśli masz zdarzenie o nazwie "kliknięto przycisk", spróbuj użyć właściwości niestandardowej o nazwie "Button Name".

## <a name="next-steps"></a>Następne kroki

* [Przegląd użycia](usage-overview.md)
* [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
* [Noszą](usage-retention.md)
* [Dodawanie niestandardowych zdarzeń do aplikacji](./api-custom-events-metrics.md)

