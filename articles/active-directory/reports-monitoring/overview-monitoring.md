---
title: Co to jest funkcja monitorowania usługi Azure Active Directory? | Microsoft Docs
description: Zawiera ogólne omówienie funkcji monitorowania usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577773"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Co to jest funkcja monitorowania usługi Azure Active Directory?

Przy użyciu funkcji monitorowania usługi Azure Active Directory (Azure AD) możesz teraz przekierowywać dzienniki aktywności usługi Azure AD do różnych punktów końcowych. Możesz następnie przechowywać je na potrzeby długoterminowego użytkowania lub zintegrować je z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) innych firm, aby uzyskać wgląd w środowisko.

Obecnie dzienniki można przekierowywać do następujących lokalizacji:

- Konto usługi Azure Storage.
- Centrum zdarzeń platformy Azure, aby możliwe było zintegrowanie z wystąpieniami usług Splunk i Sumologic.
- Obszar roboczy usługi Azure Log Analytics, w którym można przeanalizować dane oraz utworzyć pulpit nawigacyjny i alert dla określonych zdarzeń

**Rola wymagana wstępnie**: Administrator globalny

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencjonowanie i wymagania wstępne związane z raportowaniem i monitorowaniem usługi Azure AD

Potrzebujesz licencji usługi Azure AD Premium, aby uzyskać dostęp do dzienników logowania usługi Azure AD.

Aby uzyskać szczegółowe informacje dotyczące funkcji i licencjonowania w [przewodniku po cenach Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Aby wdrożyć monitorowanie i raportowanie usługi Azure AD, będziesz potrzebować użytkownika, który jest administratorem globalnym lub administratorem zabezpieczeń dla dzierżawy usługi Azure AD.

W zależności od ostatecznego miejsca docelowego danych dziennika potrzebne są następujące elementy:

* Konto usługi Azure Storage, do którego masz uprawnienia ListKeys. Zalecamy używanie konta magazynu ogólnego, a nie konta magazynu obiektów blob. Informacje o cenach magazynu można znaleźć na stronie [kalkulatora cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Przestrzeń nazw Event Hubs platformy Azure do integracji z rozwiązaniami SIEM innych firm.

* Obszar roboczy usługi Azure Log Analytics do wysyłania dzienników do Azure Monitor dzienników.

## <a name="diagnostic-settings-configuration"></a>Konfiguracji ustawień diagnostyki

Aby skonfigurować ustawienia monitorowania dla dzienników aktywności usługi Azure AD, najpierw zaloguj się w witrynie [Azure Portal](https://portal.azure.com), a następnie wybierz pozycję **Azure Active Directory**. Z tego miejsca możesz uzyskać dostęp do strony konfiguracji ustawień diagnostycznych na dwa sposoby:

* Wybierz pozycję **Ustawienia diagnostyczne** w sekcji **Monitorowanie**.

    ![Ustawienia diagnostyki](./media/overview-monitoring/diagnostic-settings.png)
    
* Wybierz pozycję **Dzienniki inspekcji** lub **Logowania**, a następnie wybierz pozycję **Eksportuj ustawienia**. 

    ![Eksportowanie ustawień](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Przekierowywanie dzienników do konta magazynu

Przekierowując dzienniki do konta magazynu usługi Azure, możesz przechowywać je dłużej niż w przypadku domyślnego okresu przechowywania określonego w [zasadach przechowywania](reference-reports-data-retention.md). Dowiedz się, jak [kierować dane do konta magazynu](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

Przekierowywanie dzienników do centrum zdarzeń platformy Azure umożliwia integrację z narzędziami SIEM takimi jak Sumologic i Splunk. Ta integracja pozwala połączyć dane dzienników aktywności usługi Azure AD z innymi danymi zarządzanymi przez rozwiązania SIEM, co zapewnia lepszy wgląd w środowisko. Dowiedz się, jak [wysyłać strumieniowo dzienniki do centrum zdarzeń](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Wysyłanie dzienników do dzienników usługi Azure Monitor

[Dzienniki usługi Azure Monitor](../../azure-monitor/logs/log-query-overview.md) to rozwiązanie, które konsoliduje dane monitorowania z różnych źródeł oraz udostępnia język zapytań i aparat analityczny, które zapewniają wgląd w działanie aplikacji i zasobów. Wysyłając dzienniki aktywności usługi Azure AD do dzienników usługi Azure Monitor, możesz szybko pobierać i monitorować zebrane dane oraz wysyłać dotyczące ich alerty. Dowiedz się, jak [wysyłać dane do dzienników usługi Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md).

Możesz także zainstalować wstępnie utworzone widoki dla dzienników aktywności usługi Azure AD, aby monitorować typowe scenariusze obejmujące logowania i zdarzenia inspekcji. Dowiedz się, jak [zainstalować widoki analizy dzienników dla dzienników aktywności usługi Azure AD i używać ich](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Następne kroki

* [Dzienniki aktywności w usłudze Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Przesyłanie strumieniowe dzienników do centrum zdarzeń](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Wysyłanie dzienników do dzienników usługi Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
