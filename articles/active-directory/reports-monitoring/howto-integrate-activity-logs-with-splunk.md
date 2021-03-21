---
title: Integrowanie usługi Splunk przy użyciu Azure Monitor | Microsoft Docs
description: Dowiedz się, jak zintegrować dzienniki Azure Active Directory z usługą Splunk przy użyciu Azure Monitor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6a597d4fd58646f56e271cb6027fb46db1e26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634230"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Instrukcje: Integrowanie dzienników Azure Active Directory z Splunk za pomocą Azure Monitor

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) z usługą Splunk przy użyciu Azure Monitor. Najpierw należy skierować dzienniki do centrum zdarzeń platformy Azure, a następnie zintegrować centrum zdarzeń z Splunk.

## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z tej funkcji są potrzebne następujące elementy:

- Centrum zdarzeń platformy Azure, które zawiera dzienniki aktywności usługi Azure AD. Dowiedz się, jak [przesyłać strumieniowo dzienniki aktywności do centrum zdarzeń](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  [Dodatek Splunk dla usług Microsoft Cloud](https://splunkbase.splunk.com/app/3110/#/details). 

## <a name="integrate-azure-active-directory-logs"></a>Integrowanie dzienników Azure Active Directory 

1. Otwórz wystąpienie programu Splunk i wybierz pozycję **podsumowanie danych**.

    ![Przycisk "podsumowanie danych"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Wybierz kartę **Sourcetypes** , a następnie wybierz pozycję **Amal: aadal: Audit**

    ![Karta Sourcetypes podsumowania danych](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Dzienniki aktywności usługi Azure AD przedstawiono na poniższej ilustracji:

    ![Dzienniki aktywności](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Jeśli nie można zainstalować dodatku w wystąpieniu usługi Splunk (na przykład jeśli używasz serwera proxy lub działającego w chmurze Splunk), możesz przekazać te zdarzenia do modułu zbierającego zdarzenia w Splunk HTTP. W tym celu należy użyć tej [funkcji platformy Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), która jest wyzwalana przez nowe komunikaty w centrum zdarzeń. 
>

## <a name="next-steps"></a>Następne kroki

* [Interpretuj schemat dzienników inspekcji w Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)