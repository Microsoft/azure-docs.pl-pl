---
title: Pobierz zdarzenia zasobów w Azure App Service
description: Dowiedz się, jak pobierać zdarzenia zasobów za poorednictwem dzienników aktywności i Event Grid w aplikacji App Service.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c20028a4f84dae9d292cf855a1e164bd69864909
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574039"
---
# <a name="get-resource-events-in-azure-app-service"></a>Pobierz zdarzenia zasobów w Azure App Service

Azure App Service udostępnia wbudowane narzędzia do monitorowania stanu i kondycji zasobów. Zdarzenia zasobów pomagają zrozumieć wszelkie zmiany wprowadzone do zasobów aplikacji sieci Web i podejmować działania w razie potrzeby. Przykładami zdarzeń są: skalowanie wystąpień, aktualizacje ustawień aplikacji, ponowne uruchamianie aplikacji internetowej i wiele innych. W tym artykule opisano sposób wyświetlania [dzienników aktywności platformy Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log) i włączania [Event Grid](../event-grid/index.yml) monitorowania zdarzeń zasobów związanych z aplikacją App Service sieci Web.

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

[Wyświetlaj i pobieraj zdarzenia dziennika aktywności platformy Azure.](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Wyślij dzienniki aktywności do Event Grid

Gdy dzienniki aktywności są oparte na użytkowniku, istnieje nowa [Event Grid](../event-grid/index.yml) integracja z App Service (wersja zapoznawcza), która rejestruje zarówno akcje użytkownika, jak i zdarzenia automatyczne. Za pomocą Event Grid można skonfigurować procedurę obsługi do reagowania na wymienione zdarzenia. Przykładowo możesz użyć usługi Event Grid, aby natychmiast wyzwalać funkcję bezserwerową i uruchamiać analizę obrazu za każdym razem, gdy do kontenera magazynu obiektów blob zostanie dodane nowe zdjęcie.

Ewentualnie możesz użyć usług Event Grid i Logic Apps do przetwarzania danych w dowolnym miejscu, bez pisania kodu. Usługa Event Grid łączy źródła danych i procedury obsługi zdarzeń. Przykładowo możesz użyć usługi Event Grid, aby natychmiast wyzwalać funkcję bezserwerową i uruchamiać analizę obrazu za każdym razem, gdy do kontenera magazynu obiektów blob zostanie dodane nowe zdjęcie.

[Wyświetl właściwości i schemat zdarzeń Azure App Service.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Następne kroki
* [Wysyłanie zapytań do dzienników przy użyciu Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Jak monitorować Azure App Service](web-sites-monitor.md)
* [Rozwiązywanie problemów Azure App Service w programie Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizowanie dzienników aplikacji w usłudze HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)