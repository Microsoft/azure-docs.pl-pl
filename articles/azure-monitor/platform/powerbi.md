---
title: Importowanie danych Log Analytics platformy Azure do Power BI | Microsoft Docs
description: Power BI to oparta na chmurze usługa analizy biznesowej firmy Microsoft, która udostępnia rozbudowane wizualizacje i raporty umożliwiające analizę różnych zestawów danych.  W tym artykule opisano sposób konfigurowania i importowania danych Log Analytics do Power BI i konfigurowania ich do automatycznego odświeżania.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 53277f64c3d1b03572732157756da1fececbcd43
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184573"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Importuj Azure Monitor dane dziennika do Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) to oparta na chmurze usługa analizy biznesowej firmy Microsoft, która udostępnia rozbudowane wizualizacje i raporty umożliwiające analizę różnych zestawów danych.  Wyniki zapytania dziennika Azure Monitor można zaimportować do Power BI zestawu danych, dzięki czemu można korzystać z jego funkcji, takich jak łączenie danych z różnych źródeł i udostępnianie raportów w sieci Web i na urządzeniach przenośnych.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Omówienie
Aby zaimportować dane z [obszaru roboczego log Analytics](manage-access.md) w Azure Monitor do Power BI, należy utworzyć zestaw danych w Power BI na podstawie [zapytania dziennika](../log-query/log-query-overview.md) w Azure monitor.  Zapytanie jest uruchamiane za każdym razem, gdy zestaw danych zostanie odświeżony.  Następnie można kompilować Power BI raporty, które używają danych z zestawu danych.  Aby utworzyć zestaw danych w Power BI, należy wyeksportować zapytanie z Log Analytics do [języka Power Query (M)](/powerquery-m/power-query-m-language-specification).  Następnie należy użyć tego do utworzenia zapytania w Power BI Desktop, a następnie opublikowania go do Power BI jako zestawu danych.  Szczegóły dotyczące tego procesu opisano poniżej.

![Log Analytics do Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Eksportowanie zapytania
Zacznij od utworzenia [zapytania dziennika](../log-query/log-query-overview.md) , które zwraca dane, które mają zostać wypełnione Power BI zestawu danych.  Następnie należy wyeksportować to zapytanie do [języka Power Query (M)](/powerquery-m/power-query-m-language-specification) , który może być używany przez Power BI Desktop.

1. [Utwórz zapytanie dziennika w log Analytics](../log-query/log-analytics-tutorial.md) , aby wyodrębnić dane dla zestawu danych.
2. Wybierz pozycję **Eksportuj**  >  **Power BI zapytanie (M)**.  Spowoduje to wyeksportowanie zapytania do pliku tekstowego o nazwie **PowerBIQuery.txt**. 

    ![Eksportowanie przeszukiwania dzienników](media/powerbi/export-analytics.png)

3. Otwórz plik tekstowy i skopiuj jego zawartość.

## <a name="import-query-into-power-bi-desktop"></a>Importuj zapytanie do Power BI Desktop
Power BI Desktop to aplikacja klasyczna, która umożliwia tworzenie zestawów danych i raportów, które mogą być publikowane w programie Power BI.  Można go również użyć do utworzenia zapytania przy użyciu języka Power Query wyeksportowanego z Azure Monitor. 

1. Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/desktop/) , jeśli jeszcze tego nie zrobiono, a następnie otwórz aplikację.
2. Wybierz pozycję **Pobierz dane**  >  **puste zapytanie** , aby otworzyć nowe zapytanie.  Następnie wybierz pozycję **Edytor zaawansowany** i wklej zawartość wyeksportowanego pliku do zapytania. Kliknij przycisk **Gotowe**.

    ![Zapytanie Power BI Desktop](media/powerbi/desktop-new-query.png)

5. Zapytanie zostanie uruchomione, a jego wyniki są wyświetlane.  Może zostać wyświetlony monit o podanie poświadczeń w celu nawiązania połączenia z platformą Azure.  
6. Wpisz nazwę opisową zapytania.  Wartość domyślna to **zapytanie1**. Kliknij przycisk **Zamknij i Zastosuj** , aby dodać zestaw danych do raportu.

    ![Nazwa Power BI Desktop](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publikowanie w usłudze Power BI
Po opublikowaniu do Power BI zostanie utworzony zestaw danych i raport.  Jeśli utworzysz raport w Power BI Desktop, zostanie on opublikowany wraz z danymi.  Jeśli nie, zostanie utworzony pusty raport.  Raport można zmodyfikować w Power BI lub utworzyć nowy oparty na zestawie danych.

1. Utwórz raport na podstawie danych.  Zapoznaj się z [dokumentacją Power BI Desktop](/power-bi/desktop-report-view) , jeśli nie znasz tego programu.  
1. Gdy wszystko będzie gotowe do wysłania do Power BI, kliknij przycisk **Publikuj**.  
1. Po wyświetleniu monitu wybierz lokalizację docelową na koncie Power BI.  Jeśli nie masz określonego miejsca docelowego, użyj **obszaru mój obszar roboczy**.

    ![Power BI Desktop publikowanie](media/powerbi/desktop-publish.png)

1. Po zakończeniu publikowania kliknij pozycję **Otwórz w Power BI** , aby otworzyć Power BI z nowym zestawem danych.


### <a name="configure-scheduled-refresh"></a>Konfigurowanie odświeżania zaplanowanego
Zestaw danych utworzony w Power BI będzie miał te same dane, które wcześniej były znajdowane w Power BI Desktop.  Należy okresowo odświeżyć zestaw danych, aby ponownie uruchomić zapytanie i wypełnić je najnowszymi danymi z Azure Monitor.  

1. Kliknij obszar roboczy, do którego został przekazany raport, a następnie wybierz menu **zestawy danych** . 
1. Wybierz menu kontekstowe obok nowego zestawu danych, a następnie wybierz pozycję **Ustawienia**. 
1. W obszarze **poświadczenia źródła danych** należy mieć komunikat informujący, że poświadczenia są nieprawidłowe.  Wynika to z faktu, że nie podano jeszcze poświadczeń dla zestawu danych, który ma być używany podczas odświeżania jego danych.  
1. Kliknij pozycję **Edytuj poświadczenia** i określ poświadczenia z dostępem do obszaru roboczego Log Analytics w Azure monitor. Jeśli wymagane jest uwierzytelnianie dwuskładnikowe, wybierz opcję **OAuth2** , **Aby uzyskać** monit o zalogowanie się przy użyciu poświadczeń.

    ![Harmonogram Power BI](media/powerbi/powerbi-schedule.png)

5. W obszarze **zaplanowane odświeżanie** Włącz opcję, aby zapewnić aktualność **danych**.  Opcjonalnie można zmienić **częstotliwość odświeżania** i co najmniej jeden określony czas, aby uruchomić odświeżanie.

    ![Power BI odświeżanie](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Następne kroki
* Informacje o [przeszukiwaniu dzienników](../log-query/log-query-overview.md) do tworzenia zapytań, które można eksportować do Power BI.
* Dowiedz się więcej na temat [Power BI](https://powerbi.microsoft.com) tworzenia wizualizacji na podstawie eksportu Azure monitor dzienników.