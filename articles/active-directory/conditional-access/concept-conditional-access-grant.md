---
title: Udzielanie kontrolek w zasadach dostępu warunkowego — Azure Active Directory
description: Co to są kontrole udzielania w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bce54bb845e3085d654e3980123ef5c8a856fd98
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530201"
---
# <a name="conditional-access-grant"></a>Dostęp warunkowy: udzielanie

W ramach zasad dostępu warunkowego administrator może używać kontroli dostępu do przyznawania lub blokowania dostępu do zasobów.

![Zasady dostępu warunkowego z kontrolą udzielania wymagającą uwierzytelniania wieloskładnikowego](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokowanie dostępu

Ustawienie Blokuj uwzględnia wszelkie przypisania i uniemożliwia dostęp na podstawie konfiguracji zasad dostępu warunkowego.

Blokuj to zaawansowana kontrolka, która powinna być przechowana z odpowiednią wiedzą. Zasady z instrukcje bloku mogą mieć niezamierzone skutki uboczne. Odpowiednie testowanie i walidacja są niezbędne przed włączeniem na dużą skalę. Administratorzy powinni używać narzędzi, takich jak tryb tylko [do](concept-conditional-access-report-only.md) raportów dostępu warunkowego, i narzędzia [What If w dostępie](what-if-tool.md) warunkowym podczas dokonywania zmian.

## <a name="grant-access"></a>Udzielanie dostępu

Administratorzy mogą zdecydować się na wymuszenia jednej lub większej liczby kontrolek podczas udzielania dostępu. Te kontrolki obejmują następujące opcje: 

- [Wymagaj uwierzytelniania wieloskładnikowego (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Wymagaj, aby urządzenie było oznaczone jako zgodne (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Wymaganie urządzenia przyłączone do hybrydowej usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Wymaganie zatwierdzonej aplikacji klienckiej](app-based-conditional-access.md)
- [Wymaganie zasad ochrony aplikacji](app-protection-based-conditional-access.md)
- [Wymagaj zmiany hasła](#require-password-change)

Gdy administratorzy wybiorą opcję połączenia tych opcji, mogą wybrać następujące metody:

- Wymagaj wszystkich wybranych kontrolek **(kontrolka AND)**
- Wymagaj jednej z wybranych kontrolek **(kontrolka OR)**

Domyślnie dostęp warunkowy wymaga wszystkich wybranych kontrolek.

### <a name="require-multi-factor-authentication"></a>Wymaganie uwierzytelniania wieloskładnikowego

Zaznaczenie tego pola wyboru będzie wymagać od użytkowników wykonania uwierzytelniania wieloskładnikowego usługi Azure AD. Więcej informacji na temat wdrażania usługi Azure AD Multi-Factor Authentication można znaleźć w artykule [Planning a cloud-based Azure AD Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md)(Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication opartego na chmurze).

[Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-overview) spełnia wymaganie uwierzytelniania wieloskładnikowego w zasadach dostępu warunkowego. 

### <a name="require-device-to-be-marked-as-compliant"></a>Wymaganie, aby urządzenie było oznaczone jako zgodne

Organizacje, które wdrożyły Microsoft Intune mogą używać informacji zwróconych z urządzeń do identyfikowania urządzeń spełniających określone wymagania dotyczące zgodności. Te informacje o zgodności zasad są przekazywane z usługi Intune do usługi Azure AD, gdzie dostęp warunkowy może podejmować decyzje dotyczące przyznawania lub blokowania dostępu do zasobów. Aby uzyskać więcej informacji na temat zasad zgodności, zobacz artykuł Ustawianie reguł na urządzeniach w celu umożliwienia dostępu do zasobów w [organizacji przy użyciu usługi Intune.](/intune/protect/device-compliance-get-started)

Urządzenie może być oznaczone jako zgodne przez usługę Intune (dla dowolnego systemu operacyjnego urządzenia) lub przez system MDM innej firmy dla Windows 10 urządzeń. Jamf Pro jest jedynym obsługiwanym systemem MDM innej firmy. Więcej informacji na temat integracji można znaleźć w artykule Integrate Jamf Pro with Intune for compliance (Integracja narzędzia [Jamf Pro z usługą Intune w celu zapewnienia zgodności).](/intune/protect/conditional-access-integrate-jamf)

Urządzenia muszą być zarejestrowane w usłudze Azure AD, aby można je było oznaczać jako zgodne. Więcej informacji na temat rejestracji urządzeń można znaleźć w artykule [Co to jest tożsamość urządzenia.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Wymaganie urządzenia przyłączone do hybrydowej usługi Azure AD

Organizacje mogą zdecydować się na użycie tożsamości urządzenia w ramach swoich zasad dostępu warunkowego. Organizacje mogą wymagać, aby urządzenia zostały przyłączone do hybrydowej usługi Azure AD przy użyciu tego pola wyboru. Aby uzyskać więcej informacji na temat tożsamości urządzeń, zobacz artykuł [Co to jest tożsamość urządzenia?](../devices/overview.md).

W przypadku korzystania [z przepływu OAuth kodu](../develop/v2-oauth2-device-code.md)urządzenia nie jest obsługiwana kontrola wymaganego udzielenia urządzenia zarządzanego lub warunek stanu urządzenia. Wynika to z tego, że urządzenie wykonujące uwierzytelnianie nie może dostarczyć stanu urządzenia do urządzenia dostarczającego kod, a stan urządzenia w tokenie jest zablokowany dla urządzenia wykonującego uwierzytelnianie. Zamiast tego użyj kontrolki wymagania uwierzytelniania wieloskładnikowego.

### <a name="require-approved-client-app"></a>Wymaganie zatwierdzonej aplikacji klienckiej

Organizacje mogą wymagać, aby próba dostępu do wybranych aplikacji w chmurze została wykonana z zatwierdzonej aplikacji klienckiej. Te zatwierdzone aplikacje klienckie obsługują [zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) niezależnie od wszelkich rozwiązań do zarządzania urządzeniami przenośnymi (MDM).

Aby można było korzystać z tej kontroli przyznawania, dostęp warunkowy wymaga zarejestrowania urządzenia w Azure Active Directory, co wymaga użycia aplikacji brokera. Aplikacja brokera może być aplikacją Microsoft Authenticator dla systemu iOS albo aplikacją Microsoft Authenticator portal firmy microsoft dla urządzeń z systemem Android. Jeśli aplikacja brokera nie jest zainstalowana na urządzeniu, gdy użytkownik próbuje się uwierzytelnić, zostanie przekierowany do odpowiedniego sklepu z aplikacjami w celu zainstalowania wymaganej aplikacji brokera.

To ustawienie dotyczy następujących aplikacji dla systemów iOS i Android:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype dla firm
- Microsoft StaffHub
- Usługa Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Administrator platformy Microsoft 365

**Uwagi**

- Zatwierdzone aplikacje klienckie obsługują funkcję zarządzania aplikacjami mobilnymi usługi Intune.
- Wymaganie **zatwierdzonej aplikacji klienckiej:**
   - Obsługuje tylko warunki platformy urządzeń z systemami iOS i Android.
   - Do zarejestrowania urządzenia jest wymagana aplikacja brokera. Aplikacja brokera może być aplikacją Microsoft Authenticator dla systemu iOS albo aplikacją Microsoft Authenticator portal firmy microsoft dla urządzeń z systemem Android.
- Dostęp warunkowy nie może Microsoft Edge w trybie InPrivate zatwierdzonej aplikacji klienckiej.
- Korzystanie z usługi Azure AD serwer proxy aplikacji w celu umożliwienia aplikacji mobilnej Power BI nawiązywania połączenia z lokalną usługą Serwer raportów usługi Power BI nie jest obsługiwane w przypadku zasad dostępu warunkowego, które wymagają aplikacji Microsoft Power BI jako zatwierdzonej aplikacji klienckiej.

Zobacz artykuł [Instrukcje: Wymaganie zatwierdzonych](app-based-conditional-access.md) aplikacji klienckich w celu uzyskania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego, aby uzyskać przykłady konfiguracji.

### <a name="require-app-protection-policy"></a>Wymaganie zasad ochrony aplikacji

W zasadach dostępu warunkowego możesz wymagać, aby zasady ochrony aplikacji usługi [Intune](/intune/app-protection-policy) zostały obecne w aplikacji klienckiej, zanim dostęp będzie dostępny dla wybranych aplikacji w chmurze. 

Aby można było korzystać z tej kontroli przyznawania, dostęp warunkowy wymaga zarejestrowania urządzenia w Azure Active Directory, co wymaga użycia aplikacji brokera. Aplikacją brokera jest aplikacja Microsoft Authenticator w przypadku systemu iOS lub aplikacja Portal firmy Microsoft w przypadku urządzeń z systemem Android. Jeśli aplikacja brokera nie jest zainstalowana na urządzeniu, gdy użytkownik próbuje się uwierzytelnić, zostanie przekierowany do sklepu z aplikacjami w celu zainstalowania aplikacji brokera.

Aplikacje muszą mieć zaimplementowany zestaw **SDK usługi Intune z** **pakietem Policy Assurance** i spełniać niektóre inne wymagania dotyczące obsługi tego ustawienia. Deweloperzy wdrażający aplikacje przy użyciu zestawu SDK usługi Intune mogą znaleźć więcej informacji w dokumentacji zestawu SDK dotyczącej tych wymagań.

Następujące aplikacje klienckie zostały potwierdzone w celu obsługi tego ustawienia:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Lists (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine for Intune
- Nine Mail — kalendarz & e-mail

> [!NOTE]
> Usługi Microsoft Teams, Microsoft Kaizala, Microsoft Skype dla firm i Microsoft Visio nie obsługują udzielania zasad **Wymagaj ochrony** aplikacji. Jeśli wymagasz, aby te aplikacje działały, użyj wyłącznie uprawnienia **Wymagaj zatwierdzonych** aplikacji. Użycie klauzuli lub między dwoma grantami nie będzie działać w przypadku tych trzech aplikacji.

**Uwagi**

- Aplikacje dla zasad ochrony aplikacji obsługują funkcję zarządzania aplikacjami mobilnymi usługi Intune z ochroną zasad.
- Wymagania **dotyczące zasad ochrony** aplikacji:
    - Obsługuje tylko warunki platformy urządzeń z systemami iOS i Android.
    - Do zarejestrowania urządzenia jest wymagana aplikacja brokera. W systemie iOS aplikacja brokera jest Microsoft Authenticator, a w systemie Android — Intune — Portal firmy aplikację.

Przykłady konfiguracji można znaleźć w artykule [Instrukcje: Wymaganie](app-protection-based-conditional-access.md) zasad ochrony aplikacji i zatwierdzonej aplikacji klienckiej do uzyskiwania dostępu do aplikacji w chmurze z dostępem warunkowym.

### <a name="require-password-change"></a>Wymagaj zmiany hasła 

Po wykryciu ryzyka związanego z użytkownikiem przy użyciu warunków zasad ryzyka związanego z użytkownikiem administratorzy mogą zdecydować, aby użytkownik bezpiecznie zmienił hasło przy użyciu samoobsługowego resetowania hasła usługi Azure AD. W przypadku wykrycia ryzyka związanego z użytkownikiem użytkownicy mogą przeprowadzić samoobsługowe resetowanie hasła w celu samodzielnego korygowania, co spowoduje zamknięcie zdarzenia o ryzyku użytkownika, aby zapobiec niepotrzebnemu szumowi dla administratorów. 

Gdy użytkownik zostanie poproszony o zmianę hasła, będzie najpierw wymagany do ukończenia uwierzytelniania wieloskładnikowego. Należy upewnić się, że wszyscy użytkownicy zarejestrowali się na uwierzytelnianie wieloskładnikowe, aby przygotować się na wypadek wykrycia ryzyka dla ich konta.  

> [!WARNING]
> Użytkownicy muszą być wcześniej zarejestrowani w celu samoobsługowego resetowania hasła przed wyzwoleniem zasad ryzyka związanego z użytkownikiem. 

Istnieje kilka ograniczeń w przypadku konfigurowania zasad przy użyciu kontrolki zmiany hasła.  

1. Zasady muszą być przypisane do "wszystkich aplikacji w chmurze". Uniemożliwia to osobie atakującej użycie innej aplikacji do zmiany hasła użytkownika i zresetowania ryzyka związanego z kontem przez zalogowanie się do innej aplikacji. 
1. Nie można wymagać zmiany hasła w innych kontrolkach, takich jak wymaganie zgodnego urządzenia.  
1. Kontrolki zmiany hasła można używać tylko z warunkiem przypisania użytkownika i grupy, warunkiem przypisania aplikacji w chmurze (który musi być ustawiony na wszystkie) i warunkami ryzyka użytkownika. 

### <a name="terms-of-use"></a>Warunki użytkowania

Jeśli Organizacja utworzyła warunki użytkowania, dodatkowe opcje mogą być widoczne w obszarze kontroli udzielania. Te opcje umożliwiają administratorom wymaganie potwierdzenia warunków użytkowania jako warunku dostępu do zasobów chronionych przez zasady. Więcej informacji na temat warunków użytkowania można znaleźć w artykule, w [Azure Active Directory warunki użytkowania.](terms-of-use.md)

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: kontrolki sesji](concept-conditional-access-session.md)

- [Typowe zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb samego raportu](concept-conditional-access-report-only.md)
