---
title: Rozwiązywanie problemów z brakującymi danymi w dziennikach aktywności | Microsoft Docs
description: Zawiera rozwiązanie problemu dotyczącego braku danych w dziennikach aktywności usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c335a4d30846f7c1b4dbd6b6aedc4d100a9b43a
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74014287"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Rozwiązywanie problemów: Brak danych w dziennikach aktywności usługi Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Nie mogę znaleźć dzienników inspekcji dla ostatnich działań w witrynie Azure Portal

### <a name="symptoms"></a>Objawy

W witrynie Azure Portal wykonano pewne akcje, które powinny zostać odzwierciedlone w dziennikach inspekcji w bloku `Activity logs > Audit Logs`, ale nie można ich znaleźć.

 ![Raportowanie](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dziennikach aktywności. W poniższej tabeli wyliczono wartości opóźnień dla dzienników aktywności. 

| Raport | &nbsp; | Opóźnienie (P95) | Opóźnienie (P99) |
|--------|--------|---------------|---------------|
| Inspekcja katalogu | &nbsp; | 2 min | 5 min |
| Aktywność związana z logowaniem | &nbsp; | 2 min | 5 min | 

### <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do dwóch godzin, a następnie sprawdź, czy akcje pojawią się w dzienniku. Jeśli dzienniki nie są widoczne nawet po dwóch godzinach, [utwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), abyśmy mogli przeanalizować tę sytuację.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Nie można znaleźć ostatnich operacji logowania użytkownika w dzienniku aktywności logowania usługi Azure Active Directory

### <a name="symptoms"></a>Objawy

Po ostatnim zalogowaniu się do witryny Azure Portal oczekiwano wyświetlenia dzienników logowania dla akcji w bloku `Activity logs > Sign-ins`, ale nie można ich znaleźć.

 ![Raportowanie](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dziennikach aktywności. W poniższej tabeli wyliczono wartości opóźnień dla dzienników aktywności. 

| Raport | &nbsp; | Opóźnienie (P95) | Opóźnienie (P99) |
|--------|--------|---------------|---------------|
| Inspekcja katalogu | &nbsp; | 2 min | 5 min |
| Aktywność związana z logowaniem | &nbsp; | 2 min | 5 min | 

### <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do dwóch godzin, a następnie sprawdź, czy akcje pojawią się w dzienniku. Jeśli dzienniki nie są widoczne nawet po dwóch godzinach, [utwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), abyśmy mogli przeanalizować tę sytuację.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Nie mogę wyświetlić danych raportu obejmujących więcej niż 30 dni w witrynie Azure Portal

### <a name="symptoms"></a>Objawy

Nie mogę wyświetlić danych logowania i inspekcji obejmujących więcej niż 30 dni w witrynie Azure Portal. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Przyczyna

W zależności od licencji w obszarze akcji usługi Azure Active Directory raporty aktywności są przechowywane przez następujący okres:

| Raport           | &nbsp; |  Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Inspekcja katalogu  | &nbsp; |   7 dni     | 30 dni             | 30 dni             |
| Aktywność związana z logowaniem | &nbsp; | Niedostępne. Możesz uzyskać dostęp do własnych logowań przez 7 dni z poziomu bloku profilu użytkownika | 30 dni | 30 dni             |

Aby uzyskać więcej informacji, zobacz [Azure Active Directory report retention policies (Zasady przechowywania raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md).  

### <a name="resolution"></a>Rozwiązanie

Dostępne są dwie opcje przechowywania danych przez czas dłuższy niż 30 dni. Możesz użyć [interfejsów API raportowania usługi Azure AD](concept-reporting-api.md) do programowego pobierania danych i przechowywania ich w bazie danych. Alternatywnie możesz zintegrować dzienniki inspekcji w systemie SIEM innej firmy, takim jak Splunk lub SumoLogic.

## <a name="next-steps"></a>Następne kroki

* [Przechowywanie danych dotyczących raportowania usługi Azure AD](reference-reports-data-retention.md).
* [Azure Active Directory opóźnień raportowania](reference-reports-latencies.md).
* [Często zadawane pytania dotyczące raportowania usługi Azure Active Directory](reports-faq.md).

