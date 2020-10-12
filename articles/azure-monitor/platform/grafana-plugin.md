---
title: Monitorowanie usług i aplikacji platformy Azure przy użyciu Grafana
description: Roześlij Azure Monitor i Application Insights dane, aby można było je przeglądać w Grafana.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 23bba091628eee767fbf292a8a8d772ffab674cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073468"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorowanie usług platformy Azure w Grafana
Teraz możesz monitorować usługi i aplikacje platformy Azure z [Grafana](https://grafana.com/) przy użyciu [wtyczki źródła danych Azure monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Wtyczka zbiera dane wydajności aplikacji zebrane przez Azure Monitor, w tym różne dzienniki i metryki. Następnie można wyświetlić te dane na pulpicie nawigacyjnym Grafana.

Wykonaj następujące kroki, aby skonfigurować serwer Grafana i utworzyć pulpity nawigacyjne dla metryk i dzienników z Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Konfigurowanie serwera Grafana

### <a name="set-up-grafana-locally"></a>Skonfiguruj lokalnie Grafana
Aby skonfigurować lokalny serwer Grafana, [Pobierz i zainstaluj Grafana w środowisku lokalnym](https://grafana.com/grafana/download). Aby użyć integracji Azure Monitor wtyczki, zainstaluj program Grafana w wersji 5,3 lub nowszej.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Konfigurowanie Grafana na platformie Azure za pomocą witryny Azure Marketplace
1. Przejdź do witryny Azure Marketplace i wybierz pozycję Grafanae przez Grafana Labs.

2. Wprowadź nazwy i szczegóły. Tworzenie nowej grupy zasobów Śledź wartości wybrane dla nazwy użytkownika maszyny wirtualnej, hasła maszyny wirtualnej i hasła administratora serwera Grafana.  

3. Wybierz rozmiar maszyny wirtualnej i konto magazynu.

4. Skonfiguruj ustawienia konfiguracji sieci.

5. Wyświetl podsumowanie i wybierz pozycję **Utwórz** po zaakceptowaniu warunków użytkowania.

6. Po zakończeniu wdrożenia wybierz pozycję **Przejdź do grupy zasobów**. Zostanie wyświetlona lista nowo utworzonych zasobów.

    ![Grafana obiektów grupy zasobów](media/grafana-plugin/grafana1.png)

    W przypadku wybrania sieciowej grupy zabezpieczeń (*grafana-sieciowej grupy zabezpieczeń* w tym przypadku) można zobaczyć, że port 3000 jest używany w celu uzyskania dostępu do serwera grafana.

7. Pobierz publiczny adres IP serwera Grafana — Wróć do listy zasobów i wybierz pozycję **publiczny adres IP**.

## <a name="sign-in-to-grafana"></a>Zaloguj się do Grafana

1. Korzystając z adresu IP serwera, Otwórz stronę logowania w witrynie *http:// \<IP address\> : 3000* lub * \<DNSName> \: 3000* w przeglądarce. Chociaż 3000 jest portem domyślnym, należy zauważyć, że podczas instalacji mógł zostać wybrany inny port. Powinna zostać wyświetlona strona logowania dla skompilowanego serwera Grafana.

    ![Ekran logowania Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Zaloguj się przy użyciu nazwy użytkownika *admin* i utworzonego wcześniej hasła administratora serwera Grafana. Jeśli używasz konfiguracji lokalnej, domyślne hasło będzie mieć wartość *administrator*, a użytkownik zażądał zmiany przy pierwszym logowaniu.

## <a name="configure-data-source-plugin"></a>Konfigurowanie wtyczki źródła danych

Po pomyślnym zalogowaniu powinna zostać wyświetlona, że wtyczka Azure Monitor źródła danych została już dołączona.

![Grafana obejmuje wtyczkę Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Wybierz pozycję **Dodaj źródło danych** , aby dodać i skonfigurować Azure monitor źródło danych.

2. Wybierz nazwę źródła danych i wybierz **Azure monitor** jako typ z listy rozwijanej.

3. Tworzenie jednostki usługi — Grafana używa jednostki usługi Azure Active Directory do nawiązywania połączenia z interfejsami API Azure Monitor i zbierania danych. Aby zarządzać dostępem do zasobów platformy Azure, należy utworzyć lub użyć istniejącej nazwy głównej usługi.
    * Zapoznaj się z [tymi instrukcjami](../../active-directory/develop/howto-create-service-principal-portal.md) , aby utworzyć nazwę główną usługi. Skopiuj i Zapisz identyfikator dzierżawy (identyfikator katalogu), identyfikator klienta (Identyfikator aplikacji) i klucz tajny klienta (wartość klucza aplikacji).
    * Aby przypisać rolę czytnika do aplikacji Azure Active Directory w ramach subskrypcji, grupy zasobów lub zasobu, który ma być monitorowany, zobacz [przypisywanie aplikacji do roli](../../active-directory/develop/howto-create-service-principal-portal.md) . 
    Interfejs API Log Analytics wymaga [roli czytnika log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-reader), która obejmuje uprawnienia roli czytnika i dodaje do niej.

4. Podaj szczegóły połączenia z interfejsami API, których chcesz użyć. Możesz połączyć się ze wszystkimi lub z innymi. 
    * W przypadku nawiązywania połączenia z metrykami i dziennikami w Azure Monitor można użyć tych samych poświadczeń, wybierając **te same szczegóły co Azure monitor interfejs API**.
    * Podczas konfigurowania wtyczki możesz wskazać chmurę platformy Azure, która ma być monitorowana (publiczna, Administracja USA platformy Azure, platforma Azure (Niemcy) lub Chiny platformy Azure).
    * Jeśli używasz Application Insights, możesz również uwzględnić interfejs API Application Insights i identyfikator aplikacji, aby zbierać metryki oparte na Application Insights. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza interfejsu API i identyfikatora aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Niektóre pola źródła danych są nazywane inaczej niż w przypadku skorelowanych ustawień platformy Azure:
        > * Identyfikator dzierżawy jest IDENTYFIKATORem katalogu platformy Azure
        > * Identyfikator klienta to Azure Active Directory identyfikator aplikacji
        > * Wpis tajny klienta to Azure Active Directory wartość klucza aplikacji

5. Jeśli używasz Application Insights, możesz również uwzględnić interfejs API Application Insights i identyfikator aplikacji, aby zbierać metryki oparte na Application Insights. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza interfejsu API i identyfikatora aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Wybierz pozycję **Zapisz**, a Grafana przetestuje poświadczenia dla każdego interfejsu API. Powinien zostać wyświetlony komunikat podobny do następującego.  
    ![Zatwierdzono konfigurację źródła danych Grafana](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Grafana

1. Przejdź do strony głównej Grafana i wybierz pozycję **nowy pulpit nawigacyjny**.

2. Na nowym pulpicie nawigacyjnym wybierz **Wykres**. Możesz wypróbować inne opcje wykresów, ale w tym artykule użyto programu *Graph* jako przykładu.

3. Pusty wykres zostanie wyświetlony na pulpicie nawigacyjnym. Kliknij tytuł panelu i wybierz pozycję **Edytuj** , aby wprowadzić szczegóły danych, które mają zostać wykreślone na tym wykresie grafu.
    ![Grafana nowy Graf](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Wybierz skonfigurowane źródło danych Azure Monitor.
   * Zbieranie metryk Azure Monitor — wybierz **Azure monitor** na liście rozwijanej usługi. Zostanie wyświetlona lista selektorów, w której można wybrać zasoby i metryki do monitorowania na tym wykresie. Aby zbierać metryki z maszyny wirtualnej, użyj przestrzeni nazw **Microsoft. COMPUTE/VirtualMachines**. Po wybraniu maszyn wirtualnych i metryk można rozpocząć wyświetlanie ich danych na pulpicie nawigacyjnym.
     ![Konfiguracja grafu Grafana dla Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Zbieranie danych dziennika Azure Monitor — wybierz pozycję **Azure log Analytics** na liście rozwijanej usługi. Wybierz obszar roboczy, do którego chcesz wykonać zapytanie, i Ustaw tekst zapytania. W tym miejscu możesz skopiować dowolne zapytanie dziennika, które już istnieje, lub utworzyć nowe. Podczas wpisywania zapytania technologia IntelliSense będzie wyświetlać i sugerują opcje Autouzupełniania. Wybierz typ wizualizacji, **tabelę** **szeregów czasowych** i uruchom zapytanie.
    
     > [!NOTE]
     >
     > Domyślne zapytanie dostarczone z wtyczką używa dwóch makr: "$ __timeFilter () i $ __interval. 
     > Te makra umożliwiają Grafana dynamiczne Obliczanie zakresu czasu i ziarna czasu, gdy powiększasz część wykresu. Można usunąć te makra i użyć standardowego filtru czasu, takiego jak *TimeGenerated > temu (1 godz)*, ale oznacza to, że wykres nie obsługuje funkcji powiększania.
    
     ![Grafana Graph dla Log Analytics platformy Azure](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Poniżej znajduje się prosty pulpit nawigacyjny z dwoma wykresami. Po lewej stronie jest wyświetlany procent procesora CPU dwóch maszyn wirtualnych. Wykres po prawej stronie przedstawia transakcje na koncie usługi Azure Storage, które zostało przerwane przez typ interfejsu API transakcji.
    ![Przykład Grafana dwóch wykresów](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcjonalne: Monitoruj niestandardowe metryki na tym samym serwerze Grafana

Można również zainstalować telegraf i InfluxDB do zbierania i wykreślania zarówno niestandardowych, jak i opartych na agentach metryk tego samego wystąpienia Grafana. Istnieje wiele wtyczek źródła danych, których można użyć do przełączenia tych metryk razem na pulpicie nawigacyjnym.

Możesz również ponownie użyć tego ustawienia, aby uwzględnić metryki z serwera Prometheus. Użyj wtyczki źródła danych Prometheus w galerii wtyczki programu Grafana.

Poniżej znajdują się dobre artykuły referencyjne dotyczące korzystania z telegraf, InfluxDB, Prometheus i Docker
 - [Jak monitorować metryki systemu przy użyciu stosu ZNACZNIKÓW w systemie Ubuntu 16,04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Rozwiązanie do monitorowania dla hostów platformy Docker, kontenerów i usług kontenerowych](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Poniżej znajduje się obraz pełnego pulpitu nawigacyjnego Grafana, który zawiera metryki z Azure Monitor i Application Insights.
![Przykładowe metryki Grafana](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Zaawansowane funkcje Grafana

### <a name="variables"></a>Zmienne
Niektóre wartości zapytania można wybrać za pomocą listy rozwijanej interfejsu użytkownika i zaktualizowane w zapytaniu. Rozważmy następujące zapytanie jako przykład:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Można skonfigurować zmienną, która będzie wyświetlać wszystkie dostępne wartości **rozwiązania** , a następnie zaktualizować zapytanie, aby go użyć.
Aby utworzyć nową zmienną, kliknij przycisk Ustawienia pulpitu nawigacyjnego w prawym górnym rogu, wybierz opcję **zmienne**, a następnie **Nowy**.
Na stronie zmienna Zdefiniuj źródło danych i zapytanie do uruchomienia w celu uzyskania listy wartości.
![Grafana Konfiguruj zmienną](./media/grafana-plugin/grafana-configure-variable-dark.png)

Po utworzeniu należy dostosować zapytanie, tak aby korzystało z wybranych wartości, a Twoje wykresy będą odpowiadały odpowiednio:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana używają zmiennych](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Tworzenie list odtwarzania pulpitu nawigacyjnego

Jedną z wielu przydatnych funkcji Grafana jest lista odtwarzania pulpitu nawigacyjnego. Można utworzyć wiele pulpitów nawigacyjnych i dodać je do listy odtwarzania i skonfigurować interwał dla każdego pulpitu nawigacyjnego. Wybierz pozycję **Odtwórz** , aby zobaczyć cykl pulpitów nawigacyjnych. Możesz chcieć wyświetlić je na dużym monitorze ściany, aby udostępnić tablicę stanu dla grupy.

![Przykład listy odtwarzania Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli skonfigurowano środowisko Grafana na platformie Azure, opłaty są naliczone, gdy maszyny wirtualne działają, niezależnie od tego, czy są używane. Aby uniknąć naliczania dodatkowych opłat, wyczyść grupę zasobów utworzoną w tym artykule.

1. W menu po lewej stronie w Azure Portal kliknij pozycję **grupy zasobów** , a następnie kliknij pozycję **Grafana**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz **Grafana** w polu tekstowym, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Następne kroki
* [Przegląd metryk Azure Monitor](data-platform.md)
