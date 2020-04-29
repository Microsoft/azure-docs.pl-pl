---
title: Omówienie dzienników platformy Azure | Microsoft Docs
description: Omówienie dzienników w Azure Monitor, które zawierają rozbudowane, częste dane dotyczące operacji zasobu platformy Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77659324"
---
# <a name="overview-of-azure-platform-logs"></a>Omówienie dzienników platformy Azure
Dzienniki platformy zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. Są one generowane automatycznie, mimo że konieczne jest skonfigurowanie niektórych dzienników platformy do przechowania do co najmniej jednego miejsca docelowego, które ma zostać zachowane. Ten artykuł zawiera omówienie dzienników platformy, w tym informacje, które zapewnia i jak można je skonfigurować do zbierania i analizy.

## <a name="types-of-platform-logs"></a>Typy dzienników platformy
W poniższej tabeli wymieniono konkretne dzienniki platformy, które są dostępne na różnych warstwach platformy Azure.

| Log | Warstwa | Opis |
|:---|:---|:---|
| Dzienniki zasobów | Zasoby platformy Azure | Zapewniaj wgląd w operacje wykonywane w ramach zasobu platformy Azure ( *płaszczyzny danych*), na przykład w celu uzyskania wpisu tajnego z Key Vault lub żądania do bazy danych. Zawartość dzienników zasobów zależy od usługi platformy Azure i typu zasobu.<br><br>*Dzienniki zasobów wcześniej były nazywane dziennikami diagnostycznymi.*  |
| Dziennik aktywności | Subskrypcja platformy Azure | Zapewnia wgląd w operacje dotyczące poszczególnych zasobów platformy Azure w ramach subskrypcji z zewnątrz (*płaszczyzny zarządzania*) poza aktualizacjami Service Health zdarzeń. Użyj dziennika _aktywności, aby określić,_ kto i kiedy _mają_być wykonywane _when_ operacje zapisu (Put, post, Delete) dotyczące zasobów w ramach subskrypcji. Można także zrozumieć stan operacji i inne istotne właściwości.  Dla każdej subskrypcji platformy Azure istnieje pojedynczy dziennik aktywności. |
| Dzienniki Azure Active Directory | Dzierżawa platformy Azure |  Zawiera historię działań związanych z logowaniem i dziennika inspekcji zmian wprowadzonych w Azure Active Directory dla określonej dzierżawy. Zobacz, [co to są raporty Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md) , aby uzyskać pełny opis Azure Active Directory dzienników.   |

> [!NOTE]
> Dziennik aktywności platformy Azure jest przeznaczony głównie dla działań, które wystąpiły w Azure Resource Manager. Nie śledzi zasobów przy użyciu modelu klasycznego/frontonu reddog. Niektóre klasyczne typy zasobów mają dostawcę zasobów serwera proxy w Azure Resource Manager (na przykład Microsoft. ClassicCompute). W przypadku korzystania z klasycznego typu zasobu za pośrednictwem Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy operacje będą widoczne w dzienniku aktywności. W przypadku korzystania z klasycznego typu zasobu poza serwerem proxy Azure Resource Manager akcje są rejestrowane tylko w dzienniku operacji. Dziennik operacji można przeglądać w osobnej sekcji portalu.

![Omówienie dzienników platformy](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Wyświetlanie dzienników platformy
Dostępne są różne opcje wyświetlania i analizowania różnych dzienników platformy Azure.

- Wyświetlanie dziennika aktywności w Azure Portal i zdarzeń dostępu z programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać szczegółowe informacje [, zobacz Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](activity-log-view.md) . 
- Wyświetl Azure Active Directory raporty dotyczące zabezpieczeń i działań w Azure Portal. Zobacz, [co to są raporty Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  Aby uzyskać szczegółowe informacje.
- Dzienniki zasobów są generowane automatycznie przez obsługiwane zasoby platformy Azure, ale nie są dostępne do wyświetlania, dopóki nie zostaną wysłane do [miejsca docelowego](#destinations). 

## <a name="destinations"></a>Miejsca docelowe
Można wysłać dzienniki platformy do co najmniej jednego miejsca docelowego w poniższej tabeli, w zależności od wymagań dotyczących monitorowania. Skonfiguruj miejsca docelowe dla dzienników platformy, [tworząc ustawienie diagnostyczne](diagnostic-settings.md).

| Element docelowy | Scenariusz | Dokumentacja |
|:---|:---|:---|:---|
| Obszar roboczy usługi Log Analytics | Analizuj dzienniki z innymi danymi monitorowania i korzystaj z funkcji Azure Monitor, takich jak zapytania i alerty dzienników. | [Dziennik aktywności i dzienniki zasobów](resource-logs-collect-workspace.md)<br>[Dzienniki katalogów aktywności platformy Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Usługa Azure Storage | Archiwizuj dzienniki na potrzeby inspekcji, analizy statycznej lub kopii zapasowej. |[Dziennik aktywności i dzienniki zasobów](archive-diagnostic-logs.md)<br>[Dzienniki katalogów aktywności platformy Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum zdarzeń | Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innych firm.  |[Dziennik aktywności i dzienniki zasobów](resource-logs-stream-event-hubs.md)<br>[Dzienniki katalogów aktywności platformy Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie schematu dziennika aktywności dla różnych kategorii](activity-log-schema.md)
* [Wyświetlanie schematu dziennika zasobów dla różnych usług platformy Azure](diagnostic-logs-schema.md)
