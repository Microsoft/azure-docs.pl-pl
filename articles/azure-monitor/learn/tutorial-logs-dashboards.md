---
title: Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Azure Log Analytics | Microsoft Docs
description: Ten samouczek ułatwia zrozumienie, jak pulpity nawigacyjne Log Analytics mogą wizualizować wszystkie zapisane zapytania dziennika, co pozwala na wyświetlanie danych w środowisku.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 05/28/2020
ms.custom: mvc
ms.openlocfilehash: 880d173c6eef9d267e8860ebad805e071706de42
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186154"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics

Pulpity nawigacyjne Log Analytics mogą wizualizować wszystkie zapisane zapytania dziennika, co umożliwia znajdowanie, skorelowanie i udostępnianie danych operacyjnych IT w organizacji.  W tym samouczku opisano Tworzenie zapytania dziennika, które zostanie użyte do obsługi udostępnionego pulpitu nawigacyjnego, do którego będzie dostępny zespół pomocy technicznej w zakresie operacji IT.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie udostępnionego pulpitu nawigacyjnego w witrynie Azure Portal
> * Wizualizowanie zapytania dziennika wydajności 
> * Dodawanie zapytania dziennika do udostępnionego pulpitu nawigacyjnego 
> * Dostosowywanie kafelka na udostępnionym pulpicie nawigacyjnym

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Tworzenie udostępnionego pulpitu nawigacyjnego
Wybierz pozycję **pulpit nawigacyjny** , aby otworzyć domyślny [pulpit nawigacyjny](../../azure-portal/azure-portal-dashboards.md). Pulpit nawigacyjny będzie wyglądać inaczej niż w poniższym przykładzie.

![Pulpit nawigacyjny witryny Azure Portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

W tym miejscu możesz zebrać najważniejsze dla działu IT dane operacyjne dotyczące wszystkich zasobów platformy Azure, w tym dane telemetryczne z usługi Azure Log Analytics.  Przed przejściem do wizualizacji zapytania dziennika najpierw Utwórz pulpit nawigacyjny i udostępnij go.  Możemy skupić się na naszym przykładowym zapytaniu dziennika wydajności, które będzie renderowane jako wykres liniowy, i dodać go do pulpitu nawigacyjnego.  

> [!NOTE]
> Następujące typy wykresów są obsługiwane na pulpitach nawigacyjnych platformy Azure przy użyciu zapytań dzienników:
> - areachart
> - columnchart
> - piechart (będzie renderowany na pulpicie nawigacyjnym jako pierścień)
> - scatterchart
> - timechart

Aby utworzyć pulpit nawigacyjny, wybierz przycisk **Nowy pulpit nawigacyjny** obok nazwy bieżącego pulpitu nawigacyjnego.

![Tworzenie nowego pulpitu nawigacyjnego w witrynie Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ta akcja tworzy nowy, pusty, prywatny pulpit nawigacyjny i przenosi użytkownika do trybu dostosowania, w którym można nazwać pulpit nawigacyjny oraz dodać kafelki i zmienić ich ułożenie. Edytuj nazwę pulpitu nawigacyjnego i określ *przykładowy pulpit nawigacyjny* dla tego samouczka, a następnie wybierz pozycję **Zakończono Dostosowywanie**.<br><br> ![Zapisywanie dostosowanego pulpitu nawigacyjnego platformy Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Podczas tworzenia pulpitu nawigacyjnego jest on domyślnie prywatny, co oznacza, że użytkownik jest jedyną osobą, która może go zobaczyć. Aby uwidocznić go dla innych osób, użyj przycisku **Udostępnij**, który jest wyświetlany wraz z innymi poleceniami pulpitu nawigacyjnego.

![Udostępnianie nowego pulpitu nawigacyjnego w witrynie Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Zostanie wyświetlona prośba o wybór subskrypcji i grupy zasobów, dla których zostanie opublikowany pulpit nawigacyjny. Dla wygody środowisko publikowania w portalu skieruje Cię do wzorca, w którym umieszcza się pulpity nawigacyjne w grupie zasobów o nazwie **pulpity nawigacyjne**.  Sprawdź wybraną subskrypcję, a następnie kliknij przycisk **Publikuj**.  Dostęp do informacji wyświetlanych na pulpicie nawigacyjnym jest kontrolowany przy użyciu [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Wizualizuj zapytanie dziennika
[Log Analytics](../log-query/log-analytics-tutorial.md) to dedykowany portal używany do pracy z kwerendami dzienników i ich wynikami. Funkcje obejmują możliwość edytowania zapytania w wielu wierszach, selektywnego wykonywania kodu, kontekstowego używania funkcji IntelliSense oraz korzystania z funkcji analizy inteligentnej. W tym samouczku użyjesz Log Analytics, aby utworzyć widok wydajności w formie graficznej, zapisać go dla przyszłego zapytania i przypiąć go do udostępnionego pulpitu nawigacyjnego utworzonego wcześniej.

Otwórz Log Analytics, wybierając pozycję **dzienniki** w menu Azure monitor. Rozpoczyna się od nowego pustego zapytania.

![Strona główna](media/tutorial-logs-dashboards/homepage.png)

Wprowadź następujące zapytanie, aby zwrócić rekordy użycia procesora zarówno dla komputerów z systemem Windows, jak i Linux, pogrupowane według komputera i TimeGenerated oraz wyświetlane na wykresie wizualnym. Kliknij przycisk **Uruchom** , aby uruchomić zapytanie i wyświetlić wykres wynikający z wyników.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Zapisz zapytanie, wybierając przycisk **Zapisz** znajdujący się u góry strony.

![Zapisz zapytanie](media/tutorial-logs-dashboards/save-query.png)

W panelu sterowania **kwerendą Zapisz** nazwę, taką jak *maszyny wirtualne platformy Azure, użycie procesora* i kategorię, takie jak *pulpity nawigacyjne* , a następnie kliknij przycisk **Zapisz**.  W ten sposób można utworzyć bibliotekę typowych zapytań, których można użyć i zmodyfikować.  Na koniec przypnij go do udostępnionego pulpitu nawigacyjnego utworzonego wcześniej, wybierając przycisk **Przypnij do pulpitu nawigacyjnego** w prawym górnym rogu strony, a następnie wybierając nazwę pulpitu nawigacyjnego.

Teraz, po przypięciu zapytania do pulpitu nawigacyjnego, zauważysz, że ma ogólny tytuł, a pod nim komentarz.

![Przykładowy pulpit nawigacyjny platformy Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Zmieńmy tytuł na coś znaczącego, co będzie łatwe do zrozumienia dla osób wyświetlających zapytanie.  Kliknij przycisk Edytuj, aby dostosować tytuł i podtytuł kafelka, a następnie kliknij przycisk **Aktualizuj**.  Zostanie wyświetlony baner z prośbą o opublikowanie lub odrzucenie zmian.  Kliknij pozycję **Zapisz kopię**.  

![Ukończona konfiguracja przykładowego pulpitu nawigacyjnego](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób tworzenia pulpitu nawigacyjnego w Azure Portal i dodawania do niego zapytania dziennika.  Przejdź do następnego samouczka, aby poznać różne odpowiedzi, które można zaimplementować w oparciu o wyniki zapytania dziennika.  

> [!div class="nextstepaction"]
> [Odpowiadanie na zdarzenia przy użyciu alertów usługi Log Analytics](tutorial-response.md)