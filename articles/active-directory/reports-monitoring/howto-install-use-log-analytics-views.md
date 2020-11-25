---
title: Jak zainstalować widoki usługi log Analytics i korzystać z nich | Microsoft Docs
description: Dowiedz się, jak zainstalować widoki usługi log Analytics i korzystać z nich Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
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
ms.openlocfilehash: 168b41534f6ea6055294cc9e9cec139853904fea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013037"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalowanie i Używanie widoków usługi log Analytics na potrzeby Azure Active Directory

Widoki usługi log Analytics ułatwiają analizowanie i wyszukiwanie dzienników aktywności w usłudze Azure AD w dzierżawie usługi Azure AD. Azure Active Directory Dzienniki aktywności usługi Azure AD obejmują:

* Dzienniki inspekcji: [raport działania dzienników inspekcji](concept-audit-logs.md) umożliwia dostęp do historii każdego zadania, które jest wykonywane w dzierżawie.
* Dzienniki logowania: w raporcie dotyczącym [działań związanych z logowaniem](concept-sign-ins.md)można określić, kto wykonał zadania zgłaszane w dziennikach inspekcji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z widoków usługi log Analytics, potrzebne są:

* Log Analytics obszar roboczy w ramach subskrypcji platformy Azure. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../../azure-monitor/learn/quick-create-workspace.md).
* Najpierw wykonaj kroki umożliwiające [kierowanie dzienników aktywności usługi Azure AD do obszaru roboczego log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Pobierz widoki z [repozytorium GitHub](https://aka.ms/AADLogAnalyticsviews) na komputer lokalny.

## <a name="install-the-log-analytics-views"></a>Instalowanie widoków usługi log Analytics

1. Przejdź do obszaru roboczego Log Analytics. Aby to zrobić, najpierw przejdź do [Azure Portal](https://portal.azure.com) i wybierz pozycję **wszystkie usługi**. Wpisz **log Analytics** w polu tekstowym, a następnie wybierz pozycję **log Analytics obszary robocze**. Wybierz obszar roboczy, do którego zostały rozesłane dzienniki aktywności, w ramach wymagań wstępnych.
2. Wybierz pozycję **Projektant widoków**, wybierz pozycję **Importuj** , a następnie wybierz pozycję **Wybierz plik** , aby zaimportować widoki z komputera lokalnego.
3. Wybierz widoki pobrane z wymagań wstępnych, a następnie wybierz pozycję **Zapisz** , aby zapisać import. Zrób to dla widoku **zdarzeń aprowizacji konta usługi Azure AD** i widoku **zdarzeń logowania** .

## <a name="use-the-views"></a>Korzystanie z widoków

1. Przejdź do obszaru roboczego Log Analytics. Aby to zrobić, najpierw przejdź do [Azure Portal](https://portal.azure.com) i wybierz pozycję **wszystkie usługi**. Wpisz **log Analytics** w polu tekstowym, a następnie wybierz pozycję **log Analytics obszary robocze**. Wybierz obszar roboczy, do którego zostały rozesłane dzienniki aktywności, w ramach wymagań wstępnych.

2. Gdy jesteś w obszarze roboczym, wybierz opcję **Podsumowanie obszaru roboczego**. Powinny zostać wyświetlone następujące trzy widoki:

    * **Zdarzenia aprowizacji konta usługi Azure AD**: ten widok przedstawia raporty dotyczące aktywności związanej z obsługą inspekcji, takie jak liczba nowych użytkowników i niepowodzenia aprowizacji, liczba zaktualizowanych użytkowników i aktualizacja niepowodzeń oraz liczba użytkowników, którym cofnięto Inicjowanie obsługi administracyjnej i odpowiadające im błędy.    
    * **Zdarzenia logowania**: ten widok przedstawia najbardziej odpowiednie raporty związane z monitorowaniem aktywności, takie jak logowania według aplikacji, użytkownika, urządzenia, a także widok podsumowania, który śledzi liczbę logowań w czasie.

3. Wybierz jeden z tych widoków, aby przejść do poszczególnych raportów. Możesz również ustawić alerty dla dowolnego parametru raportu. Na przykład Skonfigurujmy alert za każdym razem, gdy wystąpi błąd logowania. Aby to zrobić, najpierw wybierz widok **zdarzenia logowania** , wybierz pozycję **Błędy logowania** w raporcie czas, a następnie wybierz pozycję **Analiza** , aby otworzyć stronę szczegóły z rzeczywistym zapytaniem za raport. 

    ![Zrzut ekranu przedstawia stronę szczegółów analizy, która zawiera zapytanie dotyczące raportu.](./media/howto-install-use-log-analytics-views/details.png)


4. Wybierz pozycję **Ustaw Alert**, a następnie wybierz pozycję za każdym razem, gdy w sekcji **kryteria alertu** **jest &lt; &gt; zdefiniowana logika wyszukiwanie w dzienniku niestandardowym** . Ponieważ chcemy alertować zawsze, gdy wystąpi błąd logowania, ustaw **próg** domyślnej logiki alertu na **1** , a następnie wybierz pozycję **gotowe**. 

    ![Konfigurowanie logiki sygnału](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Wprowadź nazwę i opis alertu i ustaw ważność na **Ostrzeżenie**.

    ![Tworzenie reguły](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Wybierz grupę akcji do alertu. Ogólnie rzecz biorąc, może to być zespół, który ma być powiadamiany za pośrednictwem wiadomości e-mail lub wiadomości SMS, lub może być zautomatyzowanym zadaniem korzystającym z elementów webhook, Runbook, Functions, Logic Apps lub zewnętrznych rozwiązań narzędzia ITSM. Dowiedz się, jak [tworzyć grupy akcji i zarządzać nimi w Azure Portal](../../azure-monitor/platform/action-groups.md).

7. Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć alert. Za każdym razem, gdy wystąpi błąd logowania, zostanie wysłany alert.

## <a name="next-steps"></a>Następne kroki

* [Jak analizować dzienniki aktywności przy użyciu dzienników Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Rozpocznij pracę z dziennikami Azure Monitor w Azure Portal](../../azure-monitor/log-query/get-started-portal.md)