---
title: Tworzenie niestandardowych pulpitów nawigacyjnych w usłudze Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący tworzenia niestandardowych pulpitów nawigacyjnych wskaźników KPI przy użyciu usługi Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperfq1
ms.openlocfilehash: 31dd33bd8805ffcc677d5f0e98e81f2fa9e91ee2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537054"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI przy użyciu usługi Azure Application Insights

W witrynie Azure Portal można utworzyć wiele pulpitów nawigacyjnych. Każdy z nich może zawierać kafelki z wizualizacją danych pochodzących z wielu zasobów platformy Azure znajdujących się w różnych subskrypcjach i grupach zasobów.  Można przypinać różne wykresy i widoki z usługi Azure Application Insights, tworząc niestandardowe pulpity nawigacyjne, które dostarczają pełny obraz kondycji i wydajności aplikacji. Ten samouczek przedstawia kroki tworzenia niestandardowego pulpitu nawigacyjnego, który zawiera różne typy danych i wizualizacji z usługi Azure Application Insights.

 Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie niestandardowego pulpitu nawigacyjnego na platformie Azure
> * Dodawanie kafelka z galerii kafelków
> * Dodawanie standardowych metryk usługi Application Insights do pulpitu nawigacyjnego
> * Dodawanie niestandardowego wykresu metryk usługi Application Insights do pulpitu nawigacyjnego
> * Dodawanie wyników zapytania dzienników (analizy) do pulpitu nawigacyjnego

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../app/asp-net.md).

> [!NOTE]
> Uprawnienia wymagane do pracy z pulpitami nawigacyjnymi zostały omówione w artykule na temat [kontroli dostępu dla pulpitów nawigacyjnych](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego

> [!WARNING]
> Jeśli przeniesiesz zasób Application Insights do innej grupy zasobów lub subskrypcji, musisz ręcznie zaktualizować pulpit nawigacyjny, usuwając stare kafelki i przypinając nowe kafelki z tego samego zasobu Application Insights w nowej lokalizacji.

Pojedynczy pulpit nawigacyjny może zawierać zasoby z wielu aplikacji, subskrypcji i grup zasobów.  Aby rozpocząć wykonywanie kroków tego samouczka, utwórz nowy pulpit nawigacyjny aplikacji.  

1. Na liście rozwijanej menu po lewej stronie Azure Portal wybierz pozycję **pulpit nawigacyjny**.

    ![Lista rozwijana menu witryny Azure Portal](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. W okienku pulpit nawigacyjny wybierz kolejno pozycje **nowy pulpit** nawigacyjny i **pusty pulpit nawigacyjny**.

   ![Nowy pulpit nawigacyjny](media/tutorial-app-dashboards/new-dashboard.png)

3. Wpisz nazwę pulpitu nawigacyjnego.
4. Otwórz **galerię kafelków**, aby zobaczyć, jak wiele różnych kafelków możesz dodać do pulpitu nawigacyjnego.  Oprócz dodawania kafelków z galerii można przypinać wykresy i inne widoki bezpośrednio z Application Insights do pulpitu nawigacyjnego.
5. Znajdź kafelek **Markdown** i przeciągnij go na pulpit nawigacyjny.  Ten kafelek umożliwia dodanie tekstu sformatowanego w promocji, który jest idealnym rozwiązaniem do dodawania tekstu opisowego do pulpitu nawigacyjnego. Aby dowiedzieć się więcej, zobacz temat [Używanie kafelka promocji na pulpitach nawigacyjnych platformy Azure do wyświetlania zawartości niestandardowej](../../azure-portal/azure-portal-markdown-tile.md).
6. Dodaj tekst we właściwościach kafelka i zmień jego rozmiar na kanwie pulpitu nawigacyjnego.

    [![Edytowanie kafelka Markdown](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

7. Wybierz pozycję **gotowe Dostosowywanie** w górnej części ekranu, aby zakończyć tryb dostosowywania kafelka.

## <a name="add-health-overview"></a>Dodawanie ogólnych informacji o kondycji

Pulpit nawigacyjny z tekstem statycznym nie jest bardzo interesujący, więc teraz Dodaj kafelek z Application Insights, aby wyświetlić informacje o aplikacji. Kafelki usługi Application Insights można dodawać z galerii kafelków lub przypinać je bezpośrednio z ekranów usługi Application Insights. Pozwala to konfigurować znane wykresy i widoki przed przypięciem ich do pulpitu nawigacyjnego.  Na początku dodamy przegląd standardowych informacji o kondycji aplikacji.  Nie wymaga on konfiguracji i udostępnia minimalne możliwości dostosowania na pulpicie nawigacyjnym.


1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
2. W okienku **Przegląd** wybierz ikonę pinezki ikona ![ pinezki, ](media/tutorial-app-dashboards/pushpin.png) Aby dodać kafelek do pulpitu nawigacyjnego.
3. Na karcie "Przypnij do pulpitu nawigacyjnego" Wybierz pulpit nawigacyjny, do którego chcesz dodać kafelek, lub Utwórz nowy.
 
3. W prawym górnym rogu zostanie wyświetlone powiadomienie, że kafelek został przypięty do pulpitu nawigacyjnego.  Wybierz **przypięty do pulpitu nawigacyjnego** w powiadomieniu, aby powrócić do pulpitu nawigacyjnego lub użyć okienka pulpitu nawigacyjnego.
4. Kafelek jest teraz dodany do pulpitu nawigacyjnego. Wybierz pozycję **Edytuj**, aby zmienić położenie kafelka. Wybierz i przeciągnij ją do pozycji, a następnie wybierz pozycję **gotowe do dostosowania**. Teraz pulpit nawigacyjny zawiera kafelek z przydatnymi informacjami.

    [![Pulpit nawigacyjny w trybie edycji](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Dodawanie niestandardowego wykresu metryki

Panel **Metryki** umożliwia graficzne przedstawienie metryki zebranej przez usługę Application Insights w określonym czasie oraz udostępnia opcjonalne filtry i grupowanie.  Podobnie jak w przypadku innych elementów usługi Application Insights, można dodać ten wykres do pulpitu nawigacyjnego.  Wymaga to jednak wykonania pewnych czynności w zakresie dostosowania.

1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
1. Wybierz pozycję **Metryki**.  
2. Ponieważ pusty wykres już istnieje, zostanie wyświetlony monit o dodanie metryki.  Dodaj metrykę do wykresu. Opcjonalnie dodaj filtr i grupowanie.  Poniższy przykład przedstawia liczbę żądań serwera, pogrupowanych według ich powodzenia.  Dzięki temu na bieżąco widać, które żądania zakończyły się powodzeniem, a które niepowodzeniem.

    [![Dodawanie metryki](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** po prawej stronie.

3.  W prawym górnym rogu zostanie wyświetlone powiadomienie, że kafelek został przypięty do pulpitu nawigacyjnego. Wybierz **przypięty do pulpitu nawigacyjnego** w powiadomieniu, aby powrócić do pulpitu nawigacyjnego lub użyć karty pulpitu nawigacyjnego.

4. Kafelek jest teraz dodany do pulpitu nawigacyjnego. Wybierz pozycję **Edytuj**, aby zmienić położenie kafelka. Zaznacz i przeciągnij kafelek do pozycji, a następnie wybierz pozycję **gotowe do dostosowania**.

## <a name="add-logs-query"></a>Zapytanie o dodawanie dzienników

Usługa Azure Application Insights Logs oferuje bogaty język zapytań, który pozwala analizować wszystkie zebrane Application Insights dane. Podobnie jak wykresy i inne widoki, można dodać dane wyjściowe zapytania dzienników do pulpitu nawigacyjnego.

1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
2. Wybierz pozycję **dzienniki** po lewej stronie w obszarze "monitorowanie", aby otworzyć kartę dzienniki.
3. Wpisz następujące zapytanie, które zwraca 10 najczęściej wyświetlanych stron oraz liczbę żądań dostępu do nich:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Wybierz pozycję **Uruchom** , aby sprawdzić poprawność wyników zapytania.
5. Wybierz ikonę przypinania ![Ikona przypinania](media/tutorial-app-dashboards/pushpin.png) i wybierz nazwę pulpitu nawigacyjnego.

5. Zanim wrócisz do pulpitu nawigacyjnego, Dodaj kolejną kwerendę, ale Renderuj ją jako wykres, aby zobaczyć różne sposoby wizualizacji zapytania dzienników na pulpicie nawigacyjnym. Rozpocznij od następującego zapytania, które podsumowuje 10 operacji z największą liczbą wyjątków.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Wybierz pozycję **Wykres**, a następnie wybierz opcję **Pierścieniowy**, aby zwizualizować wyniki.

    [![Wykres pierścieniowy z powyższym zapytaniem](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Wybierz ikonę przypinania ![Ikona przypinania](media/tutorial-app-dashboards/pushpin.png) w prawym górnym rogu, aby przypiąć wykres do pulpitu nawigacyjnego, a następnie powrócić do pulpitu nawigacyjnego.
7. Wyniki zapytań w wybranym formacie zostały dodane do pulpitu nawigacyjnego. Zaznacz i przeciągnij każdy do pozycji, a następnie wybierz pozycję **gotowe do dostosowania**.
8. Wybierz ikonę ołówka ![Ikona ołówka](media/tutorial-app-dashboards/pencil.png) w każdym tytule, aby nadać im opisowy tytuł.

## <a name="share-dashboard"></a>Udostępnianie pulpitu nawigacyjnego

1. W górnej części pulpitu nawigacyjnego wybierz pozycję **Udostępnij** , aby opublikować zmiany.
2. Opcjonalnie możesz zdefiniować listę użytkowników, którzy powinni mieć dostęp do pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach platformy Azure](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Kliknij pozycję **Opublikuj**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak tworzyć niestandardowe pulpity nawigacyjne, zapoznaj się z pozostałą częścią dokumentacji usługi Application Insights, w tym analizą przypadku.

> [!div class="nextstepaction"]
> [Szczegółowa diagnostyka](../app/devops.md)
