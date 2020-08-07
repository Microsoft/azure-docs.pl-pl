---
title: Tworzenie niestandardowych pulpitów nawigacyjnych w usłudze Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący tworzenia niestandardowych pulpitów nawigacyjnych wskaźników KPI przy użyciu usługi Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 49984349ce8d9d1bc43778c0042dd21485eded15
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829683"
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
Pojedynczy pulpit nawigacyjny może zawierać zasoby z wielu aplikacji, subskrypcji i grup zasobów.  Aby rozpocząć wykonywanie kroków tego samouczka, utwórz nowy pulpit nawigacyjny aplikacji.  

1. W okienku pulpitów nawigacyjnych wybierz pozycję **Nowy pulpit nawigacyjny**.

   ![Nowy pulpit nawigacyjny](media/tutorial-app-dashboards/1newdashboard.png)

1. Wpisz nazwę pulpitu nawigacyjnego.
1. Otwórz **galerię kafelków**, aby zobaczyć, jak wiele różnych kafelków możesz dodać do pulpitu nawigacyjnego.  Oprócz dodawania kafelków z galerii można przypinać wykresy i inne widoki bezpośrednio z Application Insights do pulpitu nawigacyjnego.
1. Znajdź kafelek **Markdown** i przeciągnij go na pulpit nawigacyjny.  Ten kafelek umożliwia dodanie tekstu sformatowanego w promocji, który jest idealnym rozwiązaniem do dodawania tekstu opisowego do pulpitu nawigacyjnego.
1. Dodaj tekst we właściwościach kafelka i zmień jego rozmiar na kanwie pulpitu nawigacyjnego.
    
    ![Edytowanie kafelka Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kliknij pozycję **Zakończono dostosowywanie** w górnej części ekranu, aby wyjść z trybu dostosowywania kafelka.

## <a name="add-health-overview"></a>Dodawanie ogólnych informacji o kondycji
Pulpit nawigacyjny z tekstem statycznym nie jest bardzo interesujący, więc teraz Dodaj kafelek z Application Insights, aby wyświetlić informacje o aplikacji.  Kafelki usługi Application Insights można dodawać z galerii kafelków lub przypinać je bezpośrednio z ekranów usługi Application Insights.  Pozwala to konfigurować znane wykresy i widoki przed przypięciem ich do pulpitu nawigacyjnego.  Na początku dodamy przegląd standardowych informacji o kondycji aplikacji.  Nie wymaga on konfiguracji i udostępnia minimalne możliwości dostosowania na pulpicie nawigacyjnym.


1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
2. W okienku **Przegląd** kliknij ikonę Przypnij ikonę ![ pinezki, ](media/tutorial-app-dashboards/pushpin.png) Aby dodać kafelek do ostatniego oglądanego pulpitu nawigacyjnego.  
 
3. W prawym górnym rogu zostanie wyświetlone powiadomienie, że kafelek został przypięty do pulpitu nawigacyjnego. Kliknij pozycję **Przypięto do pulpitu nawigacyjnego** w powiadomieniu, aby wrócić do pulpitu nawigacyjnego lub użyć okienka pulpitów nawigacyjnych.
4. Kafelek jest teraz dodany do pulpitu nawigacyjnego. Wybierz pozycję **Edytuj**, aby zmienić położenie kafelka. Kliknij go i przeciągnij w odpowiednie miejsce, a następnie kliknij pozycję **Zakończono dostosowywanie**. Teraz pulpit nawigacyjny zawiera kafelek z przydatnymi informacjami.

    ![Pulpit nawigacyjny z osią czasu przeglądu](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Dodawanie niestandardowego wykresu metryki
Panel **Metryki** umożliwia graficzne przedstawienie metryki zebranej przez usługę Application Insights w określonym czasie oraz udostępnia opcjonalne filtry i grupowanie.  Podobnie jak w przypadku innych elementów usługi Application Insights, można dodać ten wykres do pulpitu nawigacyjnego.  Wymaga to jednak wykonania pewnych czynności w zakresie dostosowania.

1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
1. Wybierz pozycję **Metryki**.  
2. Ponieważ pusty wykres już istnieje, zostanie wyświetlony monit o dodanie metryki.  Dodaj metrykę do wykresu. Opcjonalnie dodaj filtr i grupowanie.  Poniższy przykład przedstawia liczbę żądań serwera, pogrupowanych według ich powodzenia.  Dzięki temu na bieżąco widać, które żądania zakończyły się powodzeniem, a które niepowodzeniem.

    ![Dodawanie metryki](media/tutorial-app-dashboards/metrics.png)

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** po prawej stronie. Spowoduje to dodanie widoku do ostatnio wyświetlanego pulpitu nawigacyjnego.

3.  W prawym górnym rogu zostanie wyświetlone powiadomienie, że kafelek został przypięty do pulpitu nawigacyjnego. Kliknij pozycję **Przypięto do pulpitu nawigacyjnego** w powiadomieniu, aby wrócić do pulpitu nawigacyjnego lub użyć bloku pulpitów nawigacyjnych.

4. Kafelek jest teraz dodany do pulpitu nawigacyjnego. Wybierz pozycję **Edytuj**, aby zmienić położenie kafelka. Kliknij go i przeciągnij w odpowiednie miejsce, a następnie kliknij pozycję **Zakończono dostosowywanie**.

## <a name="add-logs-analytics-query"></a>Dodaj zapytania dzienników (Analytics)
Usługa Azure Application Insights Logs (analiza) oferuje bogaty język zapytań, który pozwala analizować wszystkie zebrane Application Insights dane. Podobnie jak wykresy i inne widoki, można dodać dane wyjściowe zapytania dzienników do pulpitu nawigacyjnego.

Ponieważ dzienniki usługi Azure Application Insights (analiza) to osobna usługa, należy udostępnić pulpit nawigacyjny, aby zawierał zapytanie dzienników. Po udostępnieniu pulpitu nawigacyjnego platformy Azure jest on publikowany jako zasób platformy Azure, który może być dostępny dla innych użytkowników i zasobów.  

1. W górnej części ekranu pulpitu nawigacyjnego kliknij pozycję **Udostępnij**.

    ![Publikowanie pulpitu nawigacyjnego](media/tutorial-app-dashboards/8dashboard-share.png)

2. Nie zmieniaj zawartości pola **Nazwa pulpitu nawigacyjnego**. Wybierz pozycję **Nazwa subskrypcji**, aby udostępnić pulpit nawigacyjny.  Kliknij przycisk **Opublikuj**.  Od tej chwili pulpit nawigacyjny jest dostępny dla innych usług i subskrypcji.  Opcjonalnie możesz zdefiniować listę użytkowników, którzy powinni mieć dostęp do pulpitu nawigacyjnego.
1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
2. Kliknij pozycję **dzienniki (analiza)** po lewej stronie w obszarze monitorowanie, aby otworzyć Portal dzienniki (analiza).
3. Wpisz następujące zapytanie, które zwraca 10 najczęściej wyświetlanych stron oraz liczbę żądań dostępu do nich:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Kliknij pozycję **Uruchom**, aby zweryfikować wyniki zapytania.
5. Kliknij ikonę pinezki ![Ikona przypinania](media/tutorial-app-dashboards/pushpin.png) i wybierz nazwę pulpitu nawigacyjnego. Ta opcja umożliwia wybranie pulpitu nawigacyjnego w przeciwieństwie do poprzednich kroków, w których był używany ostatni pulpit nawigacyjny, ponieważ konsola dzienniki (analiza) jest oddzielną usługą i musi wybrać wszystkie dostępne udostępnione pulpity nawigacyjne.

5. Zanim wrócisz do pulpitu nawigacyjnego, Dodaj kolejne zapytanie, ale ten czas będzie renderowany jako wykres, aby zobaczyć różne sposoby wizualizacji zapytania dzienników na pulpicie nawigacyjnym. Rozpocznij od następującego zapytania, które podsumowuje 10 operacji z największą liczbą wyjątków.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Wybierz pozycję **Wykres**, a następnie wybierz opcję **Pierścieniowy**, aby zwizualizować wyniki.

    ![Wykres dzienników (analiza)](media/tutorial-app-dashboards/11querychart.png)

6. Kliknij ikonę pinezki ![Ikona przypinania](media/tutorial-app-dashboards/pushpin.png) w prawym górnym rogu, aby przypiąć wykres do pulpitu nawigacyjnego, a następnie wybierz link, aby powrócić do pulpitu nawigacyjnego.
4. Wyniki zapytań w wybranym formacie zostały dodane do pulpitu nawigacyjnego.  Przeciągnij poszczególne wykresy we właściwe miejsce, a następnie kliknij pozycję **Zakończono dostosowywanie**.
5. Wybierz ikonę ołówka ![Ikona ołówka](media/tutorial-app-dashboards/pencil.png) w każdym tytule, aby nadać im opisowy tytuł.

5. Wybierz pozycję **Udostępnij**, aby opublikować ponownie zmiany na pulpicie nawigacyjnym, który teraz zawiera różne wykresy i wizualizacje z usługi Application Insights.


## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak tworzyć niestandardowe pulpity nawigacyjne, zapoznaj się z pozostałą częścią dokumentacji usługi Application Insights, w tym analizą przypadku.

> [!div class="nextstepaction"]
> [Szczegółowa diagnostyka](../app/devops.md)