---
title: Zarządzanie ustawieniami Azure Portal i preferencjami | Microsoft Docs
description: Ustawienia domyślne Azure Portal można zmienić, aby spełniały własne preferencje. Ustawienia obejmują limit czasu sesji nieaktywnej, domyślny widok, Tryb menu, kontrast, motyw, powiadomienia i języki oraz formaty regionalne
services: azure-portal
keywords: Ustawienia, limit czasu, język, regionalne
author: mgblythe
ms.author: mblythe
ms.date: 08/05/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 34750bcc2e9913b23daa3682d4fdea736f9c7a9a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367435"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Zarządzanie ustawieniami i preferencjami witryny Azure Portal

Ustawienia domyślne Azure Portal można zmienić, aby spełniały własne preferencje. Większość ustawień jest dostępnych w menu **Ustawienia** w nagłówku strony globalnej.

![Zrzut ekranu przedstawiający globalne ikony nagłówka strony z wyróżnionymi ustawieniami](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Wybierz swoją subskrypcję domyślną

Możesz zmienić subskrypcję, która jest otwierana domyślnie po zalogowaniu się do Azure Portal. Jest to przydatne, jeśli masz podstawową subskrypcję, z którą pracujesz, ale używasz innych okresów czasu. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Filtruj listę zasobów według subskrypcji.":::

1. Wybierz ikonę filtr katalogu i subskrypcji w nagłówku strony globalnej.

1. Wybierz subskrypcje, które mają być domyślne podczas uruchamiania portalu. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Filtruj listę zasobów według subskrypcji."::: 


## <a name="choose-your-default-view"></a>Wybierz widok domyślny 

Po zalogowaniu się do Azure Portal można zmienić stronę, która jest otwierana domyślnie.

![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyróżnionym widokiem domyślnym](./media/set-preferences/default-view.png)

- Nie można dostosowywać **strony głównej** .  Wyświetla skróty do popularnych usług platformy Azure i wyświetla listę zasobów, które były ostatnio używane. Udostępniamy również przydatne linki do zasobów, takich jak Microsoft Learn i plan platformy Azure.

- Pulpity nawigacyjne można dostosować, aby utworzyć obszar roboczy przeznaczony tylko dla Ciebie. Można na przykład utworzyć pulpit nawigacyjny, który jest projektem, zadaniem lub rolą. Jeśli wybierzesz **pulpit nawigacyjny**, widok domyślny zostanie przestawiony na ostatnio używany pulpit nawigacyjny. Aby uzyskać więcej informacji, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w Azure Portal](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Wybieranie trybu menu portalu

Domyślny tryb menu portalu określa, ile miejsca menu portalu zajmie na stronie.

![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyróżnionymi motywami](./media/set-preferences/menu-mode.png)

- Gdy menu portalu jest w trybie **wysuwania** , jest ono ukryte, dopóki nie będzie potrzebne. Wybierz ikonę menu, aby otworzyć lub zamknąć menu.

- W przypadku wybrania opcji **tryb zadokowany** dla menu Portal jest on zawsze widoczny. Możesz zwinąć menu, aby zwiększyć miejsce pracy.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Wybierz motyw lub Włącz duży kontrast

Wybrany motyw ma wpływ na kolory tła i czcionki, które pojawiają się w Azure Portal. Można wybrać jeden z czterech predefiniowanych motywów kolorów. Wybierz każdą miniaturę, aby znaleźć motyw, który najlepiej odpowiada Ci.

Alternatywnie możesz wybrać jeden z motywów wysokiego kontrastu. Motywy o dużym kontraście sprawiają, że Azure Portal łatwiej odczytywać osoby, które mają upośledzenie wizualne; zastępują wszystkie inne opcje motywu.

![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyróżnionymi motywami](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Włączanie lub wyłączanie powiadomień wyskakujących

Powiadomienia są komunikatami systemowymi związanymi z bieżącą sesją. Udostępniają one informacje, takie jak Twoje bieżące saldo kredytowe, gdy właśnie utworzone zasoby staną się dostępne lub potwierdzają ostatnią akcję, na przykład. Po włączeniu powiadomień podręcznych komunikaty są wyświetlane na krótko w górnym rogu ekranu. 

Aby włączyć lub wyłączyć powiadomienia wyskakujące, zaznacz lub wyczyść pole wyboru **Włącz powiadomienia wyskakujące**.

![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyskakującymi powiadomieniami wyróżnionymi](./media/set-preferences/popup-notifications.png)

Aby odczytać wszystkie powiadomienia odebrane podczas bieżącej sesji, wybierz pozycję **powiadomienia** z nagłówka globalnego.

![Zrzut ekranu przedstawiający nagłówek globalny Azure Portal z wyróżnionymi powiadomieniami](./media/set-preferences/read-notifications.png)

Jeśli chcesz odczytywać powiadomienia z poprzednich sesji, poszukaj zdarzeń w dzienniku aktywności. Aby uzyskać więcej informacji, zobacz [Wyświetlanie dziennika aktywności](../azure-monitor/platform/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Zmień ustawienie limitu czasu nieaktywności

Ustawienie limitu czasu bezczynności pomaga chronić zasoby przed nieautoryzowanym dostępem, jeśli zapomnisz o zabezpieczeniu stacji roboczej. Gdy przestanie on być bezczynny przez jakiś czas, nastąpi automatyczne wylogowanie z sesji Azure Portal. Jako osoba indywidualna można zmienić ustawienie limitu czasu dla siebie. Jeśli jesteś administratorem, możesz ustawić go na poziomie katalogu dla wszystkich użytkowników w katalogu.

### <a name="change-your-individual-timeout-setting-user"></a>Zmień ustawienia poszczególnych limitów czasu (użytkownik)

Wybierz listę rozwijaną w obszarze **Wyloguj mnie, gdy jest nieaktywny**. Wybierz czas, po którym sesja Azure Portal zostanie wylogowana, jeśli jesteś w stanie bezczynności.

![Zrzut ekranu przedstawiający ustawienia portalu z wyróżnionymi ustawieniami nieaktywnego limitu czasu](./media/set-preferences/inactive-signout-user.png)

Zmiana zostanie zapisana automatycznie. Jeśli jesteś w stanie bezczynności, sesja Azure Portal zostanie wyszukana po upływie określonego czasu.

Jeśli administrator włączył zasadę limitu czasu braku aktywności, nadal można ustawić własne, o ile jest ono mniejsze niż ustawienie poziomu katalogu. Wybierz opcję **Zastąp zasadę limit czasu braku aktywności katalogu**, a następnie ustaw przedział czasu.

![Zrzut ekranu przedstawiający ustawienia portalu z zastępują wyróżnione ustawienie zasad limit czasu braku aktywności katalogu](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>Zmień ustawienie limitu czasu katalogu (Administrator)

Administratorzy w [roli administratora globalnego](../active-directory/roles/permissions-reference.md#global-administrator--company-administrator) mogą wymusić maksymalny czas bezczynności przed wylogowaniem sesji. Ustawienie limitu czasu nieaktywności dotyczy poziomu katalogu. Ustawienie zacznie obowiązywać w przypadku nowych sesji. Nie będzie od razu stosowana dla wszystkich użytkowników, którzy są już zalogowani. Aby uzyskać więcej informacji o katalogach, zobacz [Active Directory Domain Services Omówienie](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Jeśli jesteś administratorem globalnym i chcesz wymusić ustawienie limitu czasu bezczynności dla wszystkich użytkowników Azure Portal, wykonaj następujące czynności:

1. Wybierz pozycję tekst linku **Skonfiguruj limit czasu na poziomie katalogu**.

    ![Zrzut ekranu przedstawiający ustawienia portalu z wyróżnionym tekstem linku](./media/set-preferences/settings-admin.png)

1. Na stronie **Konfiguruj limit czasu braku aktywności na poziomie katalogu** wybierz opcję **Włącz limit czasu bezczynności dla Azure Portal** , aby włączyć ustawienie.

1. Następnie wprowadź **godziny** i **minuty** maksymalnego czasu, przez jaki użytkownik może być bezczynny, zanim sesja zostanie wylogowana automatycznie.

1. Wybierz przycisk **Zastosuj**.

    ![Zrzut ekranu przedstawiający stronę służącą do ustawiania limitu czasu nieaktywności poziomu katalogu](./media/set-preferences/configure.png)

Aby upewnić się, że zasady limitu czasu bezczynności są ustawione prawidłowo, wybierz pozycję **powiadomienia** z globalnego nagłówka strony. Sprawdź, czy na liście znajduje się powiadomienie o powodzeniu.

![Zrzut ekranu przedstawiający pomyślne powiadomienie o pomyślnym przekroczeniu limitu czasu nieaktywności na poziomie katalogu](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Przywróć ustawienia domyślne

Jeśli wprowadzono zmiany w ustawieniach Azure Portal i chcesz je odrzucić, wybierz opcję **Przywróć ustawienia domyślne**. Wszystkie zmiany wprowadzone w ustawieniach portalu zostaną utracone. Ta opcja nie ma wpływu na dostosowanie pulpitu nawigacyjnego.

![Zrzut ekranu przedstawiający Przywracanie ustawień domyślnych](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Eksportowanie ustawień użytkownika

Informacje o ustawieniach niestandardowych są przechowywane na platformie Azure. Można wyeksportować następujące dane użytkownika:

* Prywatne pulpity nawigacyjne w Azure Portal
* Ustawienia użytkownika, takie jak Ulubione subskrypcje lub katalogi, i ostatni zalogowany katalog
* Motywy i inne ustawienia portalu niestandardowego

Dobrym pomysłem jest wyeksportowanie i przejrzenie ustawień, jeśli planujesz je usunąć. Ponowne kompilowanie pulpitów nawigacyjnych lub przeprowadzenie ustawień może być czasochłonne.

Aby wyeksportować ustawienia portalu, wybierz opcję **Eksportuj wszystkie ustawienia**.

![Zrzut ekranu przedstawiający eksport ustawień](./media/set-preferences/useful-links-export-settings.png)

Eksportowanie ustawień tworzy plik *JSON* , który zawiera ustawienia użytkownika, takie jak motyw kolorów, Ulubione i prywatne pulpity nawigacyjne. Ze względu na dynamiczny charakter ustawień użytkownika i ryzyko uszkodzenia danych nie można importować ustawień z pliku *JSON* .

## <a name="delete-user-settings-and-dashboards"></a>Usuwanie ustawień użytkownika i pulpitów nawigacyjnych

Informacje o ustawieniach niestandardowych są przechowywane na platformie Azure. Można usunąć następujące dane użytkownika:

* Prywatne pulpity nawigacyjne w Azure Portal
* Ustawienia użytkownika, takie jak Ulubione subskrypcje lub katalogi, i ostatni zalogowany katalog
* Motywy i inne ustawienia portalu niestandardowego

Dobrym pomysłem jest wyeksportowanie i przejrzenie ustawień przed ich usunięciem. Ponowne kompilowanie pulpitów nawigacyjnych lub przeprowadzenie niestandardowych ustawień może być czasochłonne.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Aby usunąć ustawienia portalu, wybierz pozycję **Usuń wszystkie ustawienia i prywatne pulpity nawigacyjne**.

![Zrzut ekranu przedstawiający usuwanie ustawień](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Zmień język i ustawienia regionalne

Istnieją dwa ustawienia kontrolujące sposób wyświetlania tekstu w Azure Portal: 
- Ustawienie **języka** steruje językiem widocznym dla tekstu w Azure Portal. 

- **Format regionalny** kontroluje sposób wyświetlania dat, godzin, liczb i waluty.

Aby zmienić język, który jest używany w Azure Portal, Użyj listy rozwijanej, aby wybrać z listy dostępnych języków.

Wybór formatu regionalnego zmieni się, aby wyświetlić opcje regionalne tylko dla wybranego języka. Aby zmienić wybór automatyczny, Użyj listy rozwijanej, aby wybrać odpowiedni format regionalny.

Jeśli na przykład wybierzesz język angielski, a następnie wybierzesz opcję Stany Zjednoczone jako format regionalny, w dolarach amerykańskich zostanie wyświetlona wartość waluta. Jeśli wybierzesz język angielski, a następnie wybierzesz pozycję Europa jako format regionalny, w euro zostanie pokazana wartość waluta.

Wybierz pozycję **Zastosuj** , aby zaktualizować ustawienia język i format regionalny.

   ![Zrzut ekranu przedstawiający ustawienia języka i ustawień regionalnych](./media/set-preferences/language.png)

>[!NOTE]
>Te ustawienia językowe i regionalne mają wpływ tylko na Azure Portal. Linki do dokumentacji otwierane na nowej karcie lub w oknie Użyj ustawień języka przeglądarki, aby określić język, który ma być wyświetlany.
>

## <a name="next-steps"></a>Następne kroki

- [Skróty klawiaturowe w Azure Portal](azure-portal-keyboard-shortcuts.md)
- [Obsługiwane przeglądarki i urządzenia](azure-portal-supported-browsers-devices.md)
- [Dodawanie, usuwanie i zmiana rozmieszczenia ulubionych](azure-portal-add-remove-sort-favorites.md)
- [Tworzenie i udostępnianie niestandardowych pulpitów nawigacyjnych](azure-portal-dashboards.md)
- [Seria instruktażowych wideo dla witryny Azure Portal](azure-portal-video-series.md)
