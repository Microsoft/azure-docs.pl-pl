---
title: Omówienie dzienników platformy Azure | Microsoft Docs
description: Omówienie dzienników w Azure Monitor, które zawierają rozbudowane, częste dane dotyczące operacji zasobu platformy Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: f2b22746b7a78349580220f4aa7579876fd21c33
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074594"
---
# <a name="overview-of-azure-platform-logs"></a>Omówienie dzienników platformy Azure
Dzienniki platformy zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. Są one generowane automatycznie, mimo że konieczne jest skonfigurowanie niektórych dzienników platformy do przechowania do co najmniej jednego miejsca docelowego, które ma zostać zachowane. Ten artykuł zawiera omówienie dzienników platformy, w tym informacje, które zapewnia i jak można je skonfigurować do zbierania i analizy.

## <a name="types-of-platform-logs"></a>Typy dzienników platformy
W poniższej tabeli wymieniono konkretne dzienniki platformy, które są dostępne na różnych warstwach platformy Azure.

| Dziennik | Warstwa | Opis |
|:---|:---|:---|
| [Dzienniki zasobów](./resource-logs.md) | Zasoby platformy Azure | Zapewniaj wgląd w operacje wykonywane w ramach zasobu platformy Azure ( *płaszczyzny danych*), na przykład w celu uzyskania wpisu tajnego z Key Vault lub żądania do bazy danych. Zawartość dzienników zasobów zależy od usługi platformy Azure i typu zasobu.<br><br>*Dzienniki zasobów wcześniej były nazywane dziennikami diagnostycznymi.*  |
| [Dziennik aktywności](../essentials/activity-log.md) | Subskrypcja platformy Azure | Zapewnia wgląd w operacje dotyczące poszczególnych zasobów platformy Azure w ramach subskrypcji z zewnątrz (*płaszczyzny zarządzania*) poza aktualizacjami Service Health zdarzeń. Użyj dziennika _aktywności, aby określić,_ kto i kiedy _mają_ być wykonywane  operacje zapisu (Put, post, Delete) dotyczące zasobów w ramach subskrypcji. Dla każdej subskrypcji platformy Azure istnieje pojedynczy dziennik aktywności. |
| [Dzienniki usługi Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) | Dzierżawa platformy Azure |  Zawiera historię działań związanych z logowaniem i dziennika inspekcji zmian wprowadzonych w Azure Active Directory dla określonej dzierżawy.   |

> [!NOTE]
> Dziennik aktywności platformy Azure jest przeznaczony głównie dla działań, które wystąpiły w Azure Resource Manager. Nie śledzi zasobów przy użyciu modelu klasycznego/frontonu reddog. Niektóre klasyczne typy zasobów mają dostawcę zasobów serwera proxy w Azure Resource Manager (na przykład Microsoft. ClassicCompute). W przypadku korzystania z klasycznego typu zasobu za pośrednictwem Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy operacje będą widoczne w dzienniku aktywności. W przypadku korzystania z klasycznego typu zasobu poza serwerem proxy Azure Resource Manager akcje są rejestrowane tylko w dzienniku operacji. Dziennik operacji można przeglądać w osobnej sekcji portalu.

![Omówienie dzienników platformy](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Wyświetlanie dzienników platformy
Dostępne są różne opcje wyświetlania i analizowania różnych dzienników platformy Azure.

- Wyświetlanie dziennika aktywności w Azure Portal i zdarzeń dostępu z programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać szczegółowe informacje [, zobacz Wyświetlanie dziennika aktywności](../essentials/activity-log.md#view-the-activity-log) . 
- Wyświetl Azure Active Directory raporty dotyczące zabezpieczeń i działań w Azure Portal. Zobacz, [co to są raporty Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  Aby uzyskać szczegółowe informacje.
- Dzienniki zasobów są generowane automatycznie przez obsługiwane zasoby platformy Azure, ale nie są dostępne do wyświetlania, dopóki nie zostaną wysłane do [miejsca docelowego](#destinations). 

## <a name="destinations"></a>Miejsca docelowe
Można wysłać dzienniki platformy do co najmniej jednego miejsca docelowego w poniższej tabeli, w zależności od wymagań dotyczących monitorowania. Skonfiguruj miejsca docelowe dla dzienników platformy, [tworząc ustawienie diagnostyczne](../essentials/diagnostic-settings.md).

| Element docelowy | Opis |
|:---|:---|
| Obszar roboczy usługi Log Analytics | Analizuj dzienniki wszystkich zasobów platformy Azure i korzystaj ze wszystkich funkcji dostępnych do [Azure monitor dzienników](../logs/data-platform-logs.md) , w tym [zapytań dzienników](../logs/log-query-overview.md) i [alertów dzienników](../alerts/alerts-log.md). Przypnij wyniki zapytania dziennika do pulpitu nawigacyjnego platformy Azure lub Dołącz go w skoroszycie jako część raportu interaktywnego. | 
| Centrum zdarzeń | Wyślij dane dziennika platformy poza platformę Azure, na przykład do wieloSIEMowej lub niestandardowej platformy telemetrii. |
| Azure Storage | Archiwizuj dzienniki na potrzeby inspekcji lub tworzenia kopii zapasowych. |

- Aby uzyskać szczegółowe informacje dotyczące tworzenia ustawień diagnostycznych dla dziennika aktywności lub dzienników zasobów, zobacz [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../essentials/diagnostic-settings.md). 
- Aby uzyskać szczegółowe informacje dotyczące tworzenia ustawień diagnostycznych dla dzienników Azure Active Directory, zobacz następujące artykuły.
  - [Integrowanie dzienników usługi Azure AD z dziennikami Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Samouczek: przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Samouczek: archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej szczegółowych informacji o dzienniku aktywności](../essentials/activity-log.md)
* [Przeczytaj więcej szczegółów na temat dzienników zasobów](./resource-logs.md)
