---
title: Ustawianie alertów w usłudze Azure Application Insights
description: Otrzymuj powiadomienia o wolnym czasie odpowiedzi, wyjątkach i innych zmianach wydajności lub użycia w aplikacji sieci Web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295075"
---
# <a name="set-alerts-in-application-insights"></a>Ustawianie alertów w Application Insights

[Usługa Azure Application Insights][start] może wysyłać alerty o zmianach metryk wydajności lub użycia w aplikacji sieci Web. 

Application Insights monitoruje aktywną aplikację na wielu [platformach][platforms] , aby ułatwić diagnozowanie problemów z wydajnością i zrozumienie wzorców użycia.

Istnieje wiele typów alertów:

* [**Alerty metryk**](../../azure-monitor/platform/alerts-metric-overview.md) informują użytkownika, gdy Metryka przekroczy wartość progową dla pewnego okresu, na przykład czasy odpowiedzi, liczby wyjątków, użycie procesora CPU lub wyświetlenie strony.
* [**Alerty dzienników**](../../azure-monitor/platform/alerts-unified-log.md) są używane do opisywania alertów, w których sygnał alertu jest oparty na niestandardowej kwerendzie Kusto.
* [**Testy sieci Web**][availability] informują użytkownika, gdy witryna jest niedostępna w Internecie lub wolno reagować. [Dowiedz się więcej][availability].
* [**Proaktywna Diagnostyka**](../../azure-monitor/app/proactive-diagnostics.md) jest konfigurowana automatycznie w celu powiadomienia o nietypowych wzorcach wydajności.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Jak ustawić alert dotyczący wyjątku przy użyciu wyszukiwania w dziennikach niestandardowych

W tej sekcji opisano sposób ustawiania alertu dotyczącego wyjątku opartego na zapytaniu. Na potrzeby tego przykładu Załóżmy, że chcemy, aby alert, gdy Częstotliwość powodzeń była większa niż 10% w ciągu ostatnich 24 godzin.

1. Przejdź do zasobu usługi Application Insights w Azure Portal.
2. Po lewej stronie w obszarze Konfiguruj kliknij **alert**.

    ![Po lewej stronie w obszarze Konfiguruj alert kliknięcia](./media/alerts/1appinsightalert.png)

3. W górnej części karty alert wybierz pozycję **Nowa reguła alertu**.

     ![W górnej części karty Alert kliknij pozycję Nowa reguła alertu.](./media/alerts/2createalert.png)

4. Zasób powinien być wybrany jako zaznaczony. Aby ustawić warunek, kliknij przycisk **Dodaj warunek**.

    ![Kliknij pozycję Dodaj warunek](./media/alerts/3addcondition.png)

5. Na karcie Konfigurowanie logiki sygnału wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**

    ![Kliknij pozycję Wyszukiwanie w dzienniku niestandardowym](./media/alerts/4customlogsearch.png)

6. Na karcie wyszukiwanie w dzienniku niestandardowym wprowadź swoje zapytanie w polu "wyszukiwanie zapytania". W tym przykładzie użyjemy poniższego zapytania Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Wpisz zapytanie w polu zapytania wyszukiwania](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Te kroki można również zastosować do innych typów alertów opartych na zapytaniach. Więcej informacji na temat języka zapytań Kusto można znaleźć w arkuszu [wprowadzenie](https://docs.microsoft.com/azure/kusto/concepts/) do tego Kusto lub ten [kod SQL do Kusto Ściągawka](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. W obszarze "logika alertu" Wybierz, czy jest on oparty na liczbie wyników, czy pomiarze metryki. Następnie wybierz warunek (większe niż, równe, mniejsze niż) i próg. Podczas zmieniania tych wartości można zauważyć, że zmieni się zdanie w wersji zapoznawczej. W tym przykładzie używamy "równej".

    ![W obszarze logika alertu wybierz spośród opcji dostępnych na podstawie warunku, a następnie wpisz próg](./media/alerts/6alertlogic.png)

8. W obszarze "obliczone na podstawie wartości" Ustaw okres i częstotliwość. Ten okres musi być zgodny z wartością, która została wprowadzona dla okresu w powyższym zapytaniu. Następnie kliknij przycisk **gotowe**.

    ![Ustaw wartość opcji okres i częstotliwość u dołu, a następnie kliknij pozycję Gotowe.](./media/alerts/7evaluate.png)

9. Zobaczymy teraz utworzony przez nas warunek szacunkowy koszt miesięczny. Poniżej w obszarze ["grupy akcji"](../platform/action-groups.md) można utworzyć nową grupę lub wybrać istniejącą. Jeśli chcesz, możesz dostosować akcje.

    ![Kliknij przycisk Wybierz lub Utwórz w obszarze Grupa akcji.](./media/alerts/8actiongroup.png)

10. Na koniec Dodaj szczegóły alertu (Nazwa reguły alertu, opis, ważność). Gdy skończysz, kliknij przycisk **Utwórz regułę alertu** u dołu.

    ![W obszarze Szczegóły alertu wpisz nazwę reguły alertu, napisz opis i wybierz ważność](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Jak anulować subskrypcję powiadomień e-mail z alertami klasycznymi

Ta sekcja ma zastosowanie do **klasycznych**alertów dostępności, **alertów metryk klasycznych Application Insights**oraz do **klasycznych alertów o błędach**.

Powiadomienia e-mail dotyczące tych klasycznych alertów są wysyłane w przypadku następujących warunków:

* Twój adres e-mail jest wyświetlany w polu adresaci wiadomości e-mail w obszarze Ustawienia reguły alertu.

* Opcja wysyłania powiadomień e-mail do użytkowników przechowujących pewne role dla subskrypcji została aktywowana i masz odpowiednią rolę dla danej subskrypcji platformy Azure.

![Zrzut ekranu powiadomień o alertach](./media/alerts/alert-notification.png)

Aby lepiej kontrolować zabezpieczenia i prywatność, zwykle zalecamy, aby jawnie określić adresatów powiadomień dla alertów klasycznych w polu **Adresaci wiadomości e-mail z powiadomieniem** . Opcja powiadamiania wszystkich użytkowników przechowujących pewne role jest zapewniana w celu zapewnienia zgodności z poprzednimi wersjami.

Aby anulować subskrypcję powiadomień e-mail wygenerowanych przez określoną regułę alertu, Usuń adres e-mail z pola **adresat wiadomości e-mail z powiadomieniem** .

Jeśli Twój adres e-mail nie znajduje się na liście jawnie, zalecamy wyłączenie opcji automatycznego powiadamiania wszystkich członków niektórych ról, a zamiast tego należy wyświetlić listę wszystkich użytkowników, którzy muszą otrzymać powiadomienia dla tej reguły alertu w polu adresaci wiadomości e-mail.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje powiadomienia o alertach (klasyczny)?

Ta sekcja ma zastosowanie tylko do klasycznych alertów i pomoże zoptymalizować swoje powiadomienia o alertach, aby upewnić się, że tylko żądani adresaci otrzymają powiadomienia. Aby dowiedzieć się więcej o różnicach między [klasycznymi alertami](../platform/alerts-classic.overview.md) i nowym działaniem alertów, zapoznaj się z [artykułem przegląd alertów](../platform/alerts-overview.md). Aby kontrolować powiadamianie o alertach w nowym środowisku alertów, użyj [grup akcji](../platform/action-groups.md).

* Zalecamy użycie określonych odbiorców w przypadku klasycznych powiadomień o alertach.

* W przypadku alertów dotyczących dowolnych metryk Application Insights (w tym metryk dostępności) opcja pola wyboru **Zbiorcza/Grupa** , jeśli jest włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w ramach subskrypcji. W efekcie _Wszyscy_ użytkownicy z dostępem do subskrypcji, w której znajduje się zasób Application Insights, znajdują się w zakresie i będą otrzymywać powiadomienia.

> [!NOTE]
> Jeśli obecnie używasz opcji **zbiorczych/grupowych** pól wyboru i go wyłączysz, nie będzie można przywrócić zmiany.

Jeśli musisz powiadomić użytkowników na podstawie ich ról, Użyj nowego środowiska alertu/alertów w czasie rzeczywistym. Za pomocą [grup akcji](../platform/action-groups.md)można skonfigurować powiadomienia e-mail dla użytkowników z dowolnymi rolami współautor/właściciela/czytnika (nie razem ze sobą jako pojedynczą opcją).

## <a name="automation"></a>Automatyzacja
* [Automatyzowanie konfigurowania alertów za pomocą programu PowerShell](../../azure-monitor/app/powershell-alerts.md)
* [Używanie elementów webhook do automatyzowania reagowania na alerty](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Zobacz także
* [Testy dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatyzowanie konfigurowania alertów](../../azure-monitor/app/powershell-alerts.md)
* [Aktywna diagnostyka](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

