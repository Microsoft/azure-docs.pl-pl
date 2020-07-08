---
title: Przesyłanie strumieniowe dzienników do SumoLogic przy użyciu Azure Monitor | Microsoft Docs
description: Dowiedz się, jak zintegrować dzienniki Azure Active Directory z usługą SumoLogic przy użyciu Azure Monitor
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91c3b1216d5ced766a77bb1fb63e41eaedb8220d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608980"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrowanie dzienników Azure Active Directory z usługą SumoLogic przy użyciu Azure Monitor

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) z usługą SumoLogic przy użyciu Azure Monitor. Najpierw należy skierować dzienniki do centrum zdarzeń platformy Azure, a następnie zintegrować centrum zdarzeń z SumoLogic.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Centrum zdarzeń platformy Azure, które zawiera dzienniki aktywności usługi Azure AD. Dowiedz się, jak [przesyłać strumieniowo dzienniki aktywności do centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Kroki umożliwiające integrację dzienników usługi Azure AD z usługą SumoLogic 

1. Najpierw należy [przesłać strumieniowo dzienniki usługi Azure AD do centrum zdarzeń platformy Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Skonfiguruj wystąpienie usługi SumoLogic, aby [zbierać dzienniki dla Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Zainstaluj aplikację usługi Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) , aby korzystać z wstępnie skonfigurowanych pulpitów nawigacyjnych, które zapewniają analizę w czasie rzeczywistym środowiska.

   ![Pulpit nawigacyjny](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Następne kroki

* [Interpretuj schemat dzienników inspekcji w Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)