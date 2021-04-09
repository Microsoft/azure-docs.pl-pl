---
title: Przesyłanie strumieniowe dzienników Azure Active Directory do Azure Monitor dzienników | Microsoft Docs
description: Informacje na temat integrowania dzienników Azure Active Directory z dziennikami Azure Monitor
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
ms.openlocfilehash: 0f328ed44252f7fb314552d6d05df9806f59d972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591137"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrowanie dzienników usługi Azure AD z dziennikami Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Dzienniki Azure Monitor umożliwiają wykonywanie zapytań dotyczących danych w celu znalezienia określonych zdarzeń, analizowanie trendów i wykonywanie korelacji w różnych źródłach danych. Dzięki integracji dzienników aktywności usługi Azure AD w dziennikach Azure Monitor można teraz wykonywać zadania, takie jak:

 * Porównaj dzienniki logowania usługi Azure AD z dziennikami zabezpieczeń opublikowanymi przez Azure Security Center

 * Rozwiązywanie wąskich gardeł wydajności na stronie logowania aplikacji przez skorelowanie danych wydajności aplikacji z usługi Azure Application Insights.  

Poniższy film wideo z sesji zapłonu demonstruje zalety korzystania z dzienników Azure Monitor dla dzienników usługi Azure AD w praktycznych scenariuszach użytkownika.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) z Azure Monitor.

## <a name="supported-reports"></a>Obsługiwane raporty

Dzienniki aktywności inspekcji i dzienniki aktywności logowania można kierować do dzienników Azure Monitor w celu przeprowadzenia dalszej analizy. 

* **Dzienniki inspekcji**: [raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.
* **Dzienniki logowania**: przy użyciu [raportu działań dotyczącego logowań](concept-sign-ins.md) można określić, kto wykonał zadania zgłoszone w dziennikach inspekcji.
* **Dzienniki aprowizacji**: w przypadku [dzienników aprowizacji](../app-provisioning/application-provisioning-log-analytics.md)można monitorować, którzy użytkownicy zostali utworzeni, zaktualizowani i usunięci we wszystkich aplikacjach innych firm. 

> [!NOTE]
> Dzienniki aktywności inspekcji i logowania związane z funkcjami B2C nie są obecnie obsługiwane.
>

## <a name="prerequisites"></a>Wymagania wstępne 

Do korzystania z tej funkcji są potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.
* Log Analytics obszar roboczy w ramach subskrypcji platformy Azure. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Korzystanie z tej funkcji wymaga licencji na Azure AD — wersja Premium P1 lub P2. Aby znaleźć licencję odpowiednią do wymagań, zobacz [porównanie ogólnodostępnych funkcji w wersji bezpłatnej, podstawowej i premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Wyślij dzienniki do Azure Monitor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz pozycję  >  **Ustawienia diagnostyczne** Azure Active Directory  ->  **Dodaj ustawienie diagnostyczne**. Możesz również wybrać opcję **Eksportuj ustawienia** na stronie **dzienniki inspekcji** lub **logowania** , aby przejść do strony Konfiguracja ustawień diagnostycznych.  
    
3. W menu **Ustawienia diagnostyczne** zaznacz pole wyboru **Wyślij do log Analytics obszar roboczy** , a następnie wybierz pozycję **Konfiguruj**.

4. Wybierz obszar roboczy Log Analytics, do którego chcesz wysłać dzienniki, lub Utwórz nowy obszar roboczy w udostępnionym oknie dialogowym.  

5. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji do obszaru roboczego Log Analytics, zaznacz pole wyboru **AuditLogs** . 
    * Aby wysłać dzienniki logowania do obszaru roboczego Log Analytics, zaznacz pole wyboru **SignInLogs** .

6. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyki](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po około 15 minutach Sprawdź, czy zdarzenia są przesyłane strumieniowo do obszaru roboczego Log Analytics.

## <a name="next-steps"></a>Następne kroki

* [Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalowanie i Używanie widoków usługi log Analytics na potrzeby Azure Active Directory](howto-install-use-log-analytics-views.md)