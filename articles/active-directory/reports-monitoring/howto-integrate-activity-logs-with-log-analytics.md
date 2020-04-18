---
title: Przesyłanie strumieniowe dzienników usługi Azure Active Directory do dzienników usługi Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z dziennikami usługi Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0ef55f6f5117ffa77052118155afea716125a4
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639722"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrowanie dzienników usługi Azure AD z dziennikami usługi Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Dzienniki usługi Azure Monitor umożliwiają wykonywanie zapytań o dane w celu znajdowania określonych zdarzeń, analizowania trendów i wykonywania korelacji między różnymi źródłami danych. Dzięki integracji dzienników aktywności usługi Azure AD w dziennikach usługi Azure Monitor można teraz wykonywać zadania, takie jak:

 * Porównywanie dzienników logowania usługi Azure AD z dziennikami zabezpieczeń opublikowanymi przez usługę Azure Security Center

 * Rozwiązywanie problemów z wąskimi gardłami wydajności na stronie logowania aplikacji, korelując dane o wydajności aplikacji z usługi Azure Application Insights.  

Poniższy film z sesji Ignite pokazuje korzyści wynikające z używania dzienników usługi Azure Monitor dla dzienników usługi Azure AD w praktycznych scenariuszach użytkownika.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

W tym artykule dowiesz się, jak zintegrować dzienniki usługi Azure Active Directory (Azure AD) z usługą Azure Monitor.

## <a name="supported-reports"></a>Obsługiwane raporty

Dzienniki aktywności inspekcji i dzienniki aktywności logowania można rozsyłać do dzienników usługi Azure Monitor w celu dalszej analizy. 

* **Dzienniki inspekcji**: [raport działań dotyczący dzienników inspekcji](concept-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.
* **Dzienniki logowania**: przy użyciu [raportu działań dotyczącego logowań](concept-sign-ins.md) można określić, kto wykonał zadania zgłoszone w dziennikach inspekcji.

> [!NOTE]
> Dzienniki aktywności inspekcji i logowania związane z funkcjami B2C nie są obecnie obsługiwane.
>

## <a name="prerequisites"></a>Wymagania wstępne 

Aby używać tej funkcji, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.
* Obszar roboczy usługi Log Analytics w ramach subskrypcji platformy Azure. Dowiedz się, jak [utworzyć obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Korzystanie z tej funkcji wymaga licencji Usługi Azure AD Premium P1 lub P2. Aby znaleźć licencję odpowiednią do wymagań, zobacz [porównanie ogólnodostępnych funkcji w wersji bezpłatnej, podstawowej i premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Wysyłanie dzienników do usługi Azure Monitor

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz**ustawienia** -> diagnostyczne **usługi Azure Active Directory** > **Dodaj ustawienie diagnostyczne**. Można również wybrać **ustawienia eksportu** ze strony **Dzienniki inspekcji** lub **logowania,** aby przejść do strony konfiguracji ustawień diagnostycznych.  
    
3. W menu **Ustawienia diagnostyczne** zaznacz pole wyboru **Wyślij do usługi Log Analytics,** a następnie wybierz polecenie **Konfiguruj**.

4. Wybierz obszar roboczy usługi Log Analytics, do którego chcesz wysłać dzienniki, lub utwórz nowy obszar roboczy w podanym oknie dialogowym.  

5. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji do obszaru roboczego usługi Log Analytics, zaznacz pole wyboru **Dzienniki inspekcji.** 
    * Aby wysłać dzienniki logowania do obszaru roboczego usługi Log Analytics, zaznacz pole wyboru **SignInLogs.**

6. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyczne](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po około 15 minutach sprawdź, czy zdarzenia są przesyłane strumieniowo do obszaru roboczego usługi Log Analytics.

## <a name="next-steps"></a>Następne kroki

* [Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników usługi Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalowanie widoków analizy dzienników dla usługi Azure Active Directory i używanie ich](howto-install-use-log-analytics-views.md)
