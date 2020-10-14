---
title: Analizowanie dzienników aktywności przy użyciu dzienników Azure Monitor | Microsoft Docs
description: Dowiedz się, jak analizować Azure Active Directory dzienniki aktywności przy użyciu dzienników Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a48a72aa021a17c59adb86bece66cec966e234bd
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056144"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników Azure Monitor

Po [zintegrowaniu dzienników aktywności usługi Azure AD z dziennikami Azure monitor](howto-integrate-activity-logs-with-log-analytics.md)można wykorzystać możliwości dzienników Azure monitor, aby uzyskać wgląd w swoje środowisko. Możesz również zainstalować widoki usługi [log Analytics dla dzienników aktywności w usłudze Azure AD](howto-install-use-log-analytics-views.md) , aby uzyskać dostęp do wstępnie skompilowanych raportów dotyczących zdarzeń inspekcji i logowania w Twoim środowisku.

W tym artykule dowiesz się, jak analizować dzienniki aktywności usługi Azure AD w obszarze roboczym Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać następujące czynności, potrzebne są:

* Log Analytics obszar roboczy w ramach subskrypcji platformy Azure. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../../azure-monitor/learn/quick-create-workspace.md).
* Najpierw wykonaj kroki umożliwiające [kierowanie dzienników aktywności usługi Azure AD do obszaru roboczego log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
*  [Dostęp](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) do obszaru roboczego usługi log Analytics
* Poniższe role w Azure Active Directory (Jeśli uzyskujesz dostęp do Log Analytics za pomocą portalu Azure Active Directory)
    - Administrator zabezpieczeń
    - Czytelnik zabezpieczeń
    - Czytelnik raportów
    - Administrator globalny
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Przejdź do obszaru roboczego Log Analytics

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **dzienniki** w sekcji **monitorowanie** , aby otworzyć obszar roboczy log Analytics. Obszar roboczy zostanie otwarty z zapytaniem domyślnym.

    ![Zapytanie domyślne](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Wyświetl schemat dzienników aktywności usługi Azure AD

Dzienniki są wypychane do tabel **AuditLogs** i **SigninLogs** w obszarze roboczym. Aby wyświetlić schemat dla następujących tabel:

1. W obszarze domyślny widok zapytania w poprzedniej sekcji Wybierz pozycję **schemat** i rozwiń obszar roboczy. 

2. Rozwiń sekcję **Zarządzanie dziennikami** , a następnie rozwiń pozycję **AuditLogs** lub **SigninLogs** , aby wyświetlić schemat dziennika.
    ![Dzienniki inspekcji dzienników ](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![ logowania](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Wykonywanie zapytań względem dzienników aktywności usługi Azure AD

Teraz, gdy masz dzienniki w obszarze roboczym, możesz teraz uruchamiać zapytania względem nich. Na przykład aby uzyskać najważniejsze aplikacje używane w ostatnim tygodniu, Zastąp zapytanie domyślne następującym poleceniem i wybierz polecenie **Uruchom** .

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Aby uzyskać najważniejsze zdarzenia inspekcji z ostatniego tygodnia, użyj następującego zapytania:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Alert dotyczący danych dziennika aktywności usługi Azure AD

Możesz również skonfigurować alerty w zapytaniu. Na przykład, aby skonfigurować alert, gdy w ostatnim tygodniu użyto więcej niż 10 aplikacji:

1. W obszarze roboczym wybierz pozycję **Ustaw Alert** , aby otworzyć stronę **Tworzenie reguły** .

    ![Ustawianie alertu](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Wybierz domyślne **kryteria alertów** utworzone w alercie i zaktualizuj **próg** w domyślnej metryce do 10.

    ![Kryteria alertu](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Wprowadź nazwę i opis alertu, a następnie wybierz poziom ważności. W naszym przykładzie możemy ustawić ją na **informacyjną**.

4. Wybierz **grupę akcji** , która będzie otrzymywać alerty w przypadku wystąpienia sygnału. Możesz wybrać powiadomienie zespołu za pośrednictwem poczty e-mail lub wiadomości SMS lub można zautomatyzować akcję za pomocą elementów webhook, Azure Functions lub Logic Apps. Dowiedz się więcej na temat [tworzenia grup alertów i zarządzania nimi w Azure Portal](../../azure-monitor/platform/action-groups.md).

5. Po skonfigurowaniu alertu wybierz pozycję **Utwórz alert** , aby go włączyć. 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>Używanie wstępnie skompilowanych skoroszytów dla dzienników aktywności usługi Azure AD

Skoroszyty zawierają kilka raportów związanych z typowymi scenariuszami związanymi z inspekcją, logowaniem i inicjowaniem obsługi zdarzeń. Możesz również otrzymywać alerty dotyczące dowolnych danych zawartych w raportach, korzystając z kroków opisanych w poprzedniej sekcji.

* **Analiza aprowizacji**: w tym [skoroszycie](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-log-analytics) przedstawiono raporty dotyczące aktywności związanej z obsługą administracyjną, takie jak liczba nowych użytkowników, których zainicjowano i zainicjowanie obsługi administracyjnej, liczba zaktualizowanych użytkowników i niepowodzenia aktualizacji oraz liczba użytkowników, którym cofnięto Inicjowanie obsługi administracyjnej, i odpowiednich błędów.    
* **Zdarzenia logowania**: ten skoroszyt pokazuje najbardziej odpowiednie raporty związane z monitorowaniem działania logowania, takie jak logowania według aplikacji, użytkownika, urządzenia, a także widok podsumowania, który śledzi liczbę logowań w czasie.
* **Dostęp warunkowy szczegółowe informacje**: [skoroszyt](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) usługi Dostęp warunkowy usługi Insights i raportowania pozwala zrozumieć wpływ zasad dostępu warunkowego w organizacji w miarę upływu czasu. 

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z zapytaniami w dziennikach Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)
* [Tworzenie grup alertów i zarządzanie nimi w Azure Portal](../../azure-monitor/platform/action-groups.md)
* [Instalowanie i Używanie widoków usługi log Analytics na potrzeby Azure Active Directory](howto-install-use-log-analytics-views.md)
