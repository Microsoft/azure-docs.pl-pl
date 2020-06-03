---
title: Ustawianie preferencji Azure Portal | Microsoft Docs
description: Ustawienia domyślne Azure Portal można zmienić, aby spełniały własne preferencje. Ustawienia obejmują limit czasu sesji nieaktywnej, domyślny widok, Tryb menu, kontrast, motyw, powiadomienia i języki oraz formaty regionalne
services: azure-portal
keywords: Ustawienia, limit czasu, język, regionalne
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: a3f51c356f4476782bb830b2702a8fe87c79235c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308068"
---
# <a name="set-your-azure-portal-preferences"></a>Ustawianie preferencji witryny Azure Portal

Ustawienia domyślne Azure Portal można zmienić, aby spełniały własne preferencje. Można zmienić każde z wymienionych poniżej ustawień:

* [Limit czasu nieaktywnej sesji](#change-the-idle-duration-for-inactive-sign-out)
* [Widok domyślny](#choose-your-default-view)
* [Tryb menu portalu](#choose-a-portal-menu-mode)
* [Motyw koloru i dużego kontrastu](#choose-a-theme)
* [Powiadomienia wyskakujące](#enable-or-disable-pop-up-notifications)
* [Język i format regionalny](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Zmień ogólne ustawienia portalu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Ustawienia** z globalnego nagłówka strony.

    ![Zrzut ekranu przedstawiający globalne ikony nagłówka strony z wyróżnionymi ustawieniami](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Zmień czas bezczynności dla nieaktywnego wylogowania

Ustawienie limitu czasu bezczynności pomaga chronić zasoby przed nieautoryzowanym dostępem, jeśli zapomnisz o zabezpieczeniu stacji roboczej. Gdy przestanie on być bezczynny przez jakiś czas, nastąpi automatyczne wylogowanie z sesji Azure Portal.

Wybierz listę rozwijaną w obszarze **Wyloguj mnie, gdy jest nieaktywny**. Wybierz czas, po którym sesja Azure Portal zostanie wylogowana, jeśli jesteś w stanie bezczynności.

   ![Zrzut ekranu przedstawiający ustawienia portalu z wyróżnionymi ustawieniami nieaktywnego limitu czasu](./media/set-preferences/inactive-signout-user.png)

Zmiana zostanie zapisana automatycznie. Jeśli jesteś w stanie bezczynności, sesja Azure Portal zostanie wyszukana po upływie określonego czasu.

To ustawienie może być również wykonywane przez administratora na poziomie katalogu w celu wymuszenia maksymalnego czasu bezczynności. Jeśli administrator skonfigurował ustawienie limitu czasu na poziomie katalogu, nadal można ustawić własny nieaktywny czas wylogowania. Wybierz ustawienie czasu, które jest mniejsze niż ustawiona na poziomie katalogu.

Jeśli administrator włączył zasadę limitu czasu braku aktywności, zaznacz pole wyboru **Zastąp zasadę limit czasu braku aktywności katalogu** . Ustaw przedział czasu, który jest mniejszy niż ustawienie zasad.

   ![Zrzut ekranu przedstawiający ustawienia portalu z zastępują wyróżnione ustawienie zasad limit czasu braku aktywności katalogu](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Jeśli jesteś administratorem i chcesz wymusić nieaktywne ustawienie limitu czasu dla wszystkich użytkowników Azure Portal, zobacz [Ustawianie limitu czasu nieaktywności na poziomie katalogu dla użytkowników Azure Portal](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Wybierz widok domyślny 

Po zalogowaniu się do Azure Portal można zmienić stronę, która jest otwierana domyślnie.

   ![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyróżnionym widokiem domyślnym](./media/set-preferences/default-view.png)

Ustawienie widoku domyślnego kontroluje, który widok Azure Portal wyświetlany po zalogowaniu się. Możesz wybrać opcję otwarcia usługi Azure Home domyślnie lub widoku pulpitu nawigacyjnego.

* Nie można dostosowywać **strony głównej** .  Wyświetla skróty do popularnych usług platformy Azure i wyświetla listę zasobów, które były ostatnio używane. Udostępniamy również przydatne linki do zasobów, takich jak Microsoft Learn i plan platformy Azure.
* Pulpity nawigacyjne można dostosować, aby utworzyć obszar roboczy przeznaczony tylko dla Ciebie. Można na przykład utworzyć pulpit nawigacyjny, który jest projektem, zadaniem lub rolą. Jeśli wybierzesz **pulpit nawigacyjny**, widok domyślny zostanie przestawiony na ostatnio używany pulpit nawigacyjny.

### <a name="choose-a-portal-menu-mode"></a>Wybieranie trybu menu portalu

Domyślny tryb menu portalu określa, ile miejsca menu portalu zajmie na stronie.

* Gdy menu portalu jest w trybie **wysuwania** , jest ono ukryte, dopóki nie będzie potrzebne. Wybierz ikonę menu, aby otworzyć lub zamknąć menu.
* W przypadku wybrania opcji Tryb **zadokowany** dla menu Portal jest on zawsze widoczny. Możesz zwinąć menu, aby zwiększyć miejsce pracy. 

### <a name="choose-a-theme"></a>Wybierz motyw

Wybrany motyw ma wpływ na kolory tła i czcionki, które pojawiają się w Azure Portal. Można wybrać jeden z czterech predefiniowanych motywów kolorów. Wybierz każdą miniaturę, aby znaleźć motyw, który najlepiej odpowiada Ci.

   ![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyróżnionymi motywami](./media/set-preferences/theme.png)

Zamiast tego możesz wybrać jeden z motywów o wysokim kontraście. Ustawienia dużego kontrastu ułatwiają Azure Portal odczytywania dla osób, które mają niesparowaną wizualizację, i zastępują wszystkie inne opcje motywu. Aby uzyskać więcej informacji, zobacz [Włączanie dużego kontrastu lub Zmienianie motywu](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Włączanie lub wyłączanie powiadomień wyskakujących

Powiadomienia są komunikatami systemowymi związanymi z bieżącą sesją. Udostępniają one informacje, takie jak Twoje bieżące saldo kredytowe, gdy właśnie utworzone zasoby staną się dostępne lub potwierdzają ostatnią akcję, na przykład. Po włączeniu powiadomień podręcznych komunikaty są wyświetlane na krótko w górnym rogu ekranu. 

Aby włączyć lub wyłączyć powiadomienia wyskakujące, zaznacz lub usuń zaznaczenie pola wyboru **Włącz powiadomienia wyskakujące** .

   ![Zrzut ekranu przedstawiający ustawienia Azure Portal z wyskakującymi powiadomieniami wyróżnionymi](./media/set-preferences/popup-notifications.png)

Aby odczytać wszystkie powiadomienia odebrane podczas bieżącej sesji, wybierz pozycję **powiadomienia** z nagłówka globalnego.

   ![Zrzut ekranu przedstawiający nagłówek globalny Azure Portal z wyróżnionymi powiadomieniami](./media/set-preferences/read-notifications.png)

Jeśli chcesz odczytywać powiadomienia z poprzednich sesji, poszukaj zdarzeń w dzienniku aktywności. Aby dowiedzieć się więcej, Przeczytaj [Widok i Pobierz zdarzenia dziennika aktywności platformy Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Ustawienia w obszarze przydatne linki

Jeśli wprowadzono zmiany w ustawieniach Azure Portal i chcesz je odrzucić, wybierz opcję **Przywróć ustawienia domyślne**. Wszystkie zmiany wprowadzone w ustawieniach portalu zostaną utracone. Ta opcja nie ma wpływu na dostosowanie pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat **eksportowania wszystkich ustawień** lub **usuwania wszystkich ustawień i prywatnych pulpitów nawigacyjnych**, zobacz [Eksportowanie lub usuwanie ustawień użytkownika](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Zmień język i ustawienia regionalne

Istnieją dwa ustawienia kontrolujące sposób wyświetlania tekstu w Azure Portal. Ustawienie **języka** steruje językiem widocznym dla tekstu w Azure Portal. **Format regionalny** kontroluje sposób wyświetlania dat, godzin, liczb i waluty.

Aby zmienić język, który jest używany w Azure Portal, Użyj listy rozwijanej, aby wybrać z listy dostępnych języków.

Wybór formatu regionalnego zmieni się, aby wyświetlić opcje regionalne tylko dla wybranego języka. Aby zmienić wybór automatyczny, Użyj listy rozwijanej, aby wybrać odpowiedni format regionalny.

Jeśli na przykład wybierzesz język angielski, a następnie wybierzesz opcję Stany Zjednoczone jako format regionalny, w dolarach amerykańskich zostanie wyświetlona wartość waluta. Jeśli wybierzesz język angielski, a następnie wybierzesz pozycję Europa jako format regionalny, w euro zostanie pokazana wartość waluta.

Wybierz pozycję **Zastosuj** , aby zaktualizować ustawienia język i format regionalny.

   ![Zrzut ekranu przedstawiający ustawienia języka i ustawień regionalnych](./media/set-preferences/language.png)

>[!NOTE]
>Te ustawienia językowe i regionalne mają wpływ tylko na Azure Portal. Linki do dokumentacji, które są otwarte w nowej karcie lub oknie, będą używać ustawień języka przeglądarki w celu określenia języka do wyświetlenia.
>

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i udostępnianie niestandardowych pulpitów nawigacyjnych](azure-portal-dashboards.md)
* [Seria instruktażowych wideo dla witryny Azure Portal](azure-portal-video-series.md)
