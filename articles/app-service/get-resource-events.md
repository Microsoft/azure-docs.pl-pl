---
title: Pobierz zdarzenia zasobów w Azure App Service
description: Dowiedz się, jak pobierać zdarzenia zasobów za poorednictwem dzienników aktywności i Event Grid w aplikacji App Service.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124741"
---
# <a name="get-resource-events-in-azure-app-service"></a>Pobierz zdarzenia zasobów w Azure App Service

Azure App Service udostępnia wbudowane narzędzia do monitorowania stanu i kondycji zasobów. Zdarzenia zasobów pomagają zrozumieć wszelkie zmiany wprowadzone do zasobów aplikacji sieci Web i podejmować działania w razie potrzeby. Przykładami zdarzeń są: skalowanie wystąpień, aktualizacje ustawień aplikacji, ponowne uruchamianie aplikacji internetowej i wiele innych. W tym artykule opisano sposób wyświetlania [dzienników aktywności platformy Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) i włączania [Event Grid](https://docs.microsoft.com/azure/event-grid/) monitorowania zdarzeń zasobów związanych z aplikacją App Service sieci Web.

> [!NOTE]
> Integracja App Service z Event Grid jest w **wersji zapoznawczej**. [Aby uzyskać więcej informacji, zapoznaj się z powiadomieniem.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Wyświetlanie dzienników aktywności platformy Azure
Dzienniki aktywności platformy Azure zawierają zdarzenia zasobów emitowane przez operacje wykonywane na zasobach w ramach subskrypcji. Zarówno akcje użytkownika w szablonach Azure Portal, jak i Azure Resource Manager przyczyniają się do zdarzeń przechwytywanych przez dziennik aktywności. 

Dzienniki aktywności platformy Azure dla App Service szczegóły, takie jak:
- jakie operacje zostały wykonane na zasobach (np. w planach App Service)
- kto uruchomił operację
- gdy wystąpiła operacja
- stan operacji
- wartości właściwości, które ułatwiają badanie operacji

### <a name="what-can-you-do-with-azure-activity-logs"></a>Co możesz zrobić z dziennikami aktywności platformy Azure?

Do dzienników aktywności platformy Azure można wykonywać zapytania przy użyciu Azure Portal, programu PowerShell, interfejsu API REST lub wiersza polecenia. Dzienniki można wysłać do konta magazynu, centrum zdarzeń i Log Analytics. Możesz również analizować je w Power BI lub tworzyć alerty, aby pozostać zaktualizowane w przypadku zdarzeń zasobów.

[Wyświetlaj i pobieraj zdarzenia dziennika aktywności platformy Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Wyślij dzienniki aktywności do Event Grid

Gdy dzienniki aktywności są oparte na użytkowniku, istnieje nowa [Event Grid](https://docs.microsoft.com/azure/event-grid/) integracja z App Service (wersja zapoznawcza), która rejestruje zarówno akcje użytkownika, jak i zdarzenia automatyczne. Za pomocą Event Grid można skonfigurować procedurę obsługi do reagowania na wymienione zdarzenia. Przykładowo możesz użyć usługi Event Grid, aby natychmiast wyzwalać funkcję bezserwerową i uruchamiać analizę obrazu za każdym razem, gdy do kontenera magazynu obiektów blob zostanie dodane nowe zdjęcie.

Ewentualnie możesz użyć usług Event Grid i Logic Apps do przetwarzania danych w dowolnym miejscu, bez pisania kodu. Usługa Event Grid łączy źródła danych i procedury obsługi zdarzeń. Przykładowo możesz użyć usługi Event Grid, aby natychmiast wyzwalać funkcję bezserwerową i uruchamiać analizę obrazu za każdym razem, gdy do kontenera magazynu obiektów blob zostanie dodane nowe zdjęcie.

### <a name="supported-event-types"></a>Obsługiwane typy zdarzeń
| Typ zdarzenia |Opis|
| -----------| ------------- |
| Microsoft. Web/witryny | Webapp |
| BackupOperationCompleted |Tworzenie kopii zapasowej webapp zakończyło się pomyślnie|
| BackupOperationFailed | Nie można utworzyć kopii zapasowej webapp|
| RestoreOperationStarted |Rozpoczęto przywracanie z kopii zapasowej|
| RestoreOperationCompleted |Przywracanie z kopii zapasowej zostało ukończone pomyślnie|
| RestoreOperationFailed |Przywracanie z kopii zapasowej nie powiodło się|
| SlotSwapStarted |Rozpoczęto wymianę gniazd|
| SlotSwapCompleted |Pomyślnie zakończono wymianę gniazd|
| SlotSwapFailed |Niepowodzenie wymiany gniazd|
| SlotSwapWithPreviewStarted |Rozpoczęto wymianę gniazd z podglądem|
| SlotSwapWithPreviewCancelled |Wymiana gniazda z podglądem nie powiodła się|
| AppUpdated | |
| Uruchomieniu | Webapp został uruchomiony ponownie |
| Zatrzymano | Webapp został zatrzymany |
| ChangedAppSettings | Ustawienia aplikacji na webapp zostały zmienione |
| - | - |
| Microsoft. Web/dopuszczalna | (Plan App Service) |
| AspUpdated | Plan usługi App Service został zaktualizowany. Obiekt zdarzenia zawiera szczegółowe informacje o właściwościach, które zostały zmienione. |
| skalowanie w górę i w dół | Plan usługi App Service jest skalowany w górę lub w dół. Obiekt zdarzenia zawiera liczbę wystąpień.|


## <a name="next-steps"></a><a name="nextsteps"></a>Następne kroki
* [Wysyłanie zapytań do dzienników przy użyciu Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Jak monitorować Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w usłudze HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
