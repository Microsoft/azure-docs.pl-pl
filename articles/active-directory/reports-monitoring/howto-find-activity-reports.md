---
title: Znajdowanie raportów aktywności użytkowników w Azure Portal | Microsoft Docs
description: Dowiedz się, gdzie Azure Active Directory raporty aktywności użytkowników znajdują się w Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42afa073da9197c12e4cbd316d311a7699d9a95f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013035"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Znajdowanie raportów dotyczących aktywności w witrynie Azure Portal

W tym artykule dowiesz się, jak znaleźć w Azure Portal raporty dotyczące działań użytkownika w usłudze Azure Active Directory (Azure AD).

## <a name="audit-logs-report"></a>Raport dotyczący dzienników inspekcji

Raport dzienniki inspekcji łączy kilka raportów dotyczących działań aplikacji w jeden widok dla raportowania opartego na kontekście. Aby uzyskać dostęp do raportu dzienniki inspekcji:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Wybierz katalog z prawego górnego rogu, a następnie wybierz blok **Azure Active Directory** w okienku nawigacji po lewej stronie.
3. Wybierz pozycję **dzienniki inspekcji** w sekcji **działanie** w bloku Azure Active Directory. 

    ![Dzienniki inspekcji](./media/howto-find-activity-reports/482.png "Dzienniki inspekcji")

Raport dotyczący dzienników inspekcji konsoliduje następujące raporty:

* Raport z inspekcji
* Działania związane z resetowaniem haseł
* Działanie rejestracji resetowania hasła
* Działanie grup samoobsługi
* Zmiany nazw grup usługi Office 365
* Działanie aprowizacji konta
* Stan przerzucenia hasła
* Błędy aprowizacji kont

### <a name="filtering-on-audit-logs"></a>Filtrowanie w dziennikach inspekcji

Możesz użyć filtrowania zaawansowanego w raporcie inspekcji, aby uzyskać dostęp do określonej kategorii danych inspekcji, określając ją w filtr **kategorii** . Aby na przykład wyświetlić wszystkie działania związane z użytkownikami, wybierz kategorię **UserManagement** . 

Kategorie obejmują:

- Wszystko
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autoryzacja
- Kontakt
- Urządzenie
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Inne
- Zasady
- ResourceManagement
- RoleManagement
- UserManagement

Możesz również filtrować określoną usługę za pomocą filtru listy rozwijanej **usługi** . Aby na przykład uzyskać wszystkie zdarzenia inspekcji związane z samoobsługowym zarządzaniem hasłami, należy wybrać filtr **samoobsługowego zarządzania hasłami** .

Usługi obejmują:

- Wszystko
- Przeglądy dostępu
- Aprowizacja kont 
- Logowanie jednokrotne aplikacji
- Metody uwierzytelniania
- B2C
- Dostęp warunkowy
- Katalog podstawowy
- Zarządzanie prawami
- Identity Protection
- Zaproszeni użytkownicy
- PIM
- Samoobsługowe zarządzanie grupami
- Samoobsługowe zarządzanie hasłami
- Warunki użytkowania

## <a name="sign-ins-report"></a>Raport dotyczący logowań 

Widok **logowania** zawiera wszystkie logowania użytkowników, a także Raport **użycia aplikacji** . Informacje o użyciu aplikacji można także wyświetlić w sekcji **Zarządzanie** **aplikacjami w przedsiębiorstwie** .

Aby uzyskać dostęp do raportu logowania:

1. Przejdź do [Azure Portal](https://portal.azure.com).
2. Wybierz katalog z prawego górnego rogu, a następnie wybierz blok **Azure Active Directory** w okienku nawigacji po lewej stronie.
3. Wybierz pozycję **Signins** w sekcji **działanie** w bloku Azure Active Directory. 

    ![Widok logowania](./media/howto-find-activity-reports/483.png "Widok logowania")


### <a name="filtering-on-application-name"></a>Filtrowanie według nazwy aplikacji

Możesz użyć raportu logowania, aby wyświetlić szczegółowe informacje o użyciu aplikacji, filtrując nazwę użytkownika lub nazwę aplikacji.

![Strona filtrowania zdarzeń Sign-In](./media/howto-find-activity-reports/07.png "Strona filtrowania zdarzeń Sign-In")

## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

### <a name="anomalous-activity-reports"></a>Raporty o nietypowych działaniach

Raporty o nietypowych działaniach zawierają informacje dotyczące wykrywania zagrożeń związanych z zabezpieczeniami, które usługa Azure AD może wykrywać i raportować.

W poniższej tabeli przedstawiono raporty o nietypowych działaniach związanych z usługą Azure AD oraz odpowiednie typy wykrywania ryzyka w Azure Portal. Aby uzyskać więcej informacji, zobacz [Azure Active Directory wykrywania zagrożeń](../identity-protection/overview-identity-protection.md).  


| Raport o nietypowej aktywności usługi Azure AD |  Typ wykrywania ryzyka ochrony tożsamości|
| :--- | :--- |
| Użytkownicy z ujawnionymi poświadczeniami | Ujawnione poświadczenia |
| Nieregularne działania związane z logowaniem | Niemożliwa podróż do nietypowych lokalizacji |
| Logowania z urządzeń, które mogą być zainfekowane | Logowania z zainfekowanych urządzeń|
| Logowania z nieznanych źródeł | Logowania z anonimowych adresów IP |
| Logowania z adresów IP związanych z podejrzanymi działaniami | Logowania z adresów IP związanych z podejrzanymi działaniami |
| - | Logowania z nieznanych lokalizacji |

Następujące raporty o zabezpieczeniach nietypowej aktywności usługi Azure AD nie są uwzględniane podczas wykrywania ryzyka w Azure Portal:

* Logowania po wielokrotnych niepowodzeniach
* Logowania z wielu lokalizacji geograficznych


### <a name="detected-risk-detections"></a>Wykryte wykrycia ryzyka

Można uzyskać dostęp do raportów dotyczących wykrywania wykrytych zagrożeń w sekcji **zabezpieczenia** w bloku **Azure Active Directory** w [Azure Portal](https://portal.azure.com). Wykryte wykrywania ryzyka są śledzone w następujących raportach:   

- [Narażeni użytkownicy](../identity-protection/overview-identity-protection.md)
- [Ryzykowne logowania](../identity-protection/overview-identity-protection.md)

    ![Raporty dotyczące zabezpieczeń](./media/howto-find-activity-reports/04.png "Raporty dotyczące zabezpieczeń")

## <a name="troubleshoot-issues-with-activity-reports"></a>Rozwiązywanie problemów z raportami aktywności

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Brak danych w pobranych dziennikach aktywności

#### <a name="symptoms"></a>Objawy 

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Zrzut ekranu przedstawia przycisk Pobierz w raporcie działania.](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Przyczyna

Po pobraniu dzienników aktywności w Azure Portal ograniczamy do 250000 rekordów, posortowanych według ostatnio używanych elementów. 

#### <a name="resolution"></a>Rozwiązanie

Można wykorzystać [interfejsy API raportowania usługi Azure AD](concept-reporting-api.md), aby pobrać do miliona rekordów z dowolnego okresu.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Brak danych inspekcji dla ostatnich akcji w Azure Portal

#### <a name="symptoms"></a>Objawy

W witrynie Azure Portal wykonano pewne akcje, które powinny zostać odzwierciedlone w dziennikach inspekcji w bloku `Activity logs > Audit Logs`, ale nie można ich znaleźć.

 ![Zrzut ekranu przedstawia raport aktywności.](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dziennikach aktywności. W poniższej tabeli wyliczono wartości opóźnień dla dzienników aktywności. 

| Raport | Opóźnienie (P95) | Opóźnienie (P99) |
|--------|---------------|---------------|
| Inspekcja katalogu | 2 min | 5 min |
| Aktywność związana z logowaniem | 2 min | 5 min |

#### <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do dwóch godzin, a następnie sprawdź, czy akcje pojawią się w dzienniku. Jeśli dzienniki nie są widoczne nawet po dwóch godzinach, [utwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), abyśmy mogli przeanalizować tę sytuację.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Brak dzienników dla ostatnich logowań użytkowników w dzienniku aktywności logowania usługi Azure AD

#### <a name="symptoms"></a>Objawy

Po ostatnim zalogowaniu się do witryny Azure Portal oczekiwano wyświetlenia dzienników logowania dla akcji w bloku `Activity logs > Sign-ins`, ale nie można ich znaleźć.

 ![Zrzut ekranu przedstawia logowania do Azure Active Directory.](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dziennikach aktywności. W poniższej tabeli wyliczono wartości opóźnień dla dzienników aktywności. 

| Raport | Opóźnienie (P95) | Opóźnienie (P99) |
|--------|---------------|---------------|
| Inspekcja katalogu | 2 min | 5 min |
| Aktywność związana z logowaniem | 2 min | 5 min |

#### <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do dwóch godzin, a następnie sprawdź, czy akcje pojawią się w dzienniku. Jeśli dzienniki nie są widoczne nawet po dwóch godzinach, [utwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), abyśmy mogli przeanalizować tę sytuację.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Nie mogę wyświetlić danych raportu obejmujących więcej niż 30 dni w witrynie Azure Portal

#### <a name="symptoms"></a>Objawy

Nie mogę wyświetlić danych logowania i inspekcji obejmujących więcej niż 30 dni w witrynie Azure Portal. Dlaczego? 

 ![Zrzut ekranu przedstawia menu Data.](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Przyczyna

W zależności od licencji w obszarze akcji usługi Azure Active Directory raporty aktywności są przechowywane przez następujący okres:

| Raport           | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — wersja Premium P1 | Usługa Azure AD — wersja Premium P2 |
| ---              | ---           | ---                 | ---
| Inspekcja katalogu  | 7 dni        | 30 dni             | 30 dni             |
| Aktywność związana z logowaniem | Niedostępne. Możesz uzyskać dostęp do własnych logowań przez 7 dni z poziomu bloku profilu użytkownika | 30 dni | 30 dni             |

Aby uzyskać więcej informacji, zobacz [Azure Active Directory report retention policies (Zasady przechowywania raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Rozwiązanie

Dostępne są dwie opcje przechowywania danych przez czas dłuższy niż 30 dni. Możesz użyć [interfejsów API raportowania usługi Azure AD](concept-reporting-api.md) do programowego pobierania danych i przechowywania ich w bazie danych. Alternatywnie możesz zintegrować dzienniki inspekcji w systemie SIEM innej firmy, takim jak Splunk lub SumoLogic.

## <a name="next-steps"></a>Następne kroki

* [Przegląd dzienników inspekcji](concept-audit-logs.md)
* [Przegląd logowania](concept-sign-ins.md)
* [Omówienie ryzykownych zdarzeń](../identity-protection/overview-identity-protection.md)