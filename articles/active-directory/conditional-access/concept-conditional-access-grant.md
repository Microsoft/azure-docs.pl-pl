---
title: Udzielanie kontroli w zasadach dostępu warunkowego — Azure Active Directory
description: Co to jest przyznanie kontroli w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a779fee89d52442207358cf90c8cf87e7ef221c2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837655"
---
# <a name="conditional-access-grant"></a>Dostęp warunkowy: Udziel

W ramach zasad dostępu warunkowego administrator może korzystać z kontroli dostępu w celu udzielenia lub zablokowania dostępu do zasobów.

![Zasady dostępu warunkowego z kontrolką Grant wymagające uwierzytelniania wieloskładnikowego](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blokowanie dostępu

Zablokowanie uwzględnia wszelkie przypisania i uniemożliwia dostęp na podstawie konfiguracji zasad dostępu warunkowego.

Block to zaawansowana kontrolka, która powinna być wielded z odpowiednią wiedzą. Zasady z instrukcjami bloku mogą mieć niezamierzone efekty uboczne. Poprawne testowanie i walidacja są niezbędne przed włączeniem na dużą skalę. Administratorzy powinni korzystać z narzędzi, takich jak [tryb tylko raport dostęp warunkowy](concept-conditional-access-report-only.md) i [Narzędzie What If w przypadku dostępu warunkowego](what-if-tool.md) podczas wprowadzania zmian.

## <a name="grant-access"></a>Udzielanie dostępu

Administratorzy mogą zdecydować się na wymuszenie co najmniej jednej kontrolki podczas udzielania dostępu. Te kontrolki obejmują następujące opcje: 

- [Wymagaj uwierzytelniania wieloskładnikowego (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Wymagaj, aby urządzenie było oznaczone jako zgodne (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Wymaganie zatwierdzonej aplikacji klienckiej](app-based-conditional-access.md)
- [Wymaganie zasad ochrony aplikacji](app-protection-based-conditional-access.md)
- [Wymagaj zmiany hasła](#require-password-change)

Gdy administratorzy zdecydują się połączyć te opcje, mogą wybrać następujące metody:

- Wymagaj wszystkich zaznaczonych kontrolek (kontrolka **i** kontrola)
- Wymagaj jednej z wybranych kontrolek (kontrolki **lub** kontrolka)

Domyślnie dostęp warunkowy wymaga wszystkich zaznaczonych kontrolek.

### <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Zaznaczenie tego pola wyboru spowoduje, że użytkownicy będą musieli wykonać Multi-Factor Authentication usługi Azure AD. Więcej informacji na temat wdrażania usługi Azure AD Multi-Factor Authentication można znaleźć w artykule [Planowanie wdrożenia usługi Azure ad Multi-Factor Authentication opartego na chmurze](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Wymagaj, aby urządzenie było oznaczone jako zgodne

Organizacje, które wdrożyły Microsoft Intune mogą korzystać z informacji zwróconych z urządzeń w celu identyfikowania urządzeń spełniających określone wymagania w zakresie zgodności. Te informacje o zgodności z tymi zasadami są przekazywane z usługi Intune w usłudze Azure AD, gdzie dostęp warunkowy może podejmować decyzje o udzieleniu lub zablokowaniu dostępu do zasobów. Aby uzyskać więcej informacji na temat zasad zgodności, zobacz artykuł [Ustawianie reguł na urządzeniach w celu zezwalania na dostęp do zasobów w organizacji przy użyciu usługi Intune](/intune/protect/device-compliance-get-started).

Urządzenie może być oznaczone jako zgodne przez usługę Intune (dla dowolnego systemu operacyjnego urządzenia) lub przez system MDM innej firmy dla urządzeń z systemem Windows 10. Jamf Pro jest jedynym obsługiwanym systemem zarządzania urządzeniami przenośnymi innej firmy. Więcej informacji na temat integracji można znaleźć w artykule integracja usługi [Jamf Pro z usługą Intune w celu zapewnienia zgodności](/intune/protect/conditional-access-integrate-jamf).

Aby można było oznaczyć je jako zgodne, urządzenia muszą być zarejestrowane w usłudze Azure AD. Więcej informacji na temat rejestracji urządzeń można znaleźć w artykule [co to jest tożsamość urządzenia](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Wymagaj hybrydowego urządzenia dołączonego do usługi Azure AD

Organizacje mogą zdecydować się na użycie tożsamości urządzenia jako części zasad dostępu warunkowego. Organizacje mogą wymagać, aby urządzenia były dołączone do hybrydowej usługi Azure AD przy użyciu tego pola wyboru. Aby uzyskać więcej informacji o tożsamościach urządzeń, zobacz artykuł [co to jest tożsamość urządzenia?](../devices/overview.md).

W przypadku korzystania z [przepływu OAuth przy użyciu kodu urządzenia](../develop/v2-oauth2-device-code.md)nie jest obsługiwana kontrola Wymagaj zarządzanego urządzenia lub stanu urządzenia. Wynika to z faktu, że urządzenie wykonujące uwierzytelnianie nie może dostarczyć stanu urządzenia do urządzenia dostarczającego kod, a stan urządzenia w tokenie jest zablokowany na urządzeniu, na którym jest wykonywane uwierzytelnianie. Zamiast tego użyj kontrolki Wymagaj uwierzytelniania wieloskładnikowego.

### <a name="require-approved-client-app"></a>Wymaganie zatwierdzonej aplikacji klienckiej

Organizacje mogą wymagać od zatwierdzonej aplikacji klienckiej próby dostępu do wybranych aplikacji w chmurze. Te zatwierdzone aplikacje klienckie obsługują [Zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) niezależnie od dowolnego rozwiązania do zarządzania urządzeniami przenośnymi (MDM).

Aby można było korzystać z tej kontroli dotacji, dostęp warunkowy wymaga zarejestrowania urządzenia w Azure Active Directory, które wymaga użycia aplikacji brokera. Aplikacja brokera może być Microsoft Authenticator dla systemu iOS lub Microsoft Authenticator lub Portal firmy Microsoft dla urządzeń z systemem Android. Jeśli aplikacja brokera nie jest zainstalowana na urządzeniu podczas próby uwierzytelnienia użytkownika, użytkownik zostanie przekierowany do odpowiedniego sklepu App/Play w celu zainstalowania wymaganej aplikacji brokera.

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
- Program Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**Uwagi**

- Zatwierdzone aplikacje klienckie obsługują funkcję zarządzania aplikacjami mobilnymi w usłudze Intune.
- Wymagane wymagania dotyczące **zatwierdzonej aplikacji klienckiej** :
   - Obsługuje tylko warunek platformy dla systemów iOS i Android.
   - Aby zarejestrować urządzenie, wymagana jest aplikacja brokera. W systemie iOS aplikacja brokera jest Microsoft Authenticator i w systemie Android jest Intune — Portal firmy App.
- Dostęp warunkowy nie może rozważyć Microsoft Edge w trybie InPrivate w zatwierdzonej aplikacji klienckiej.

Zapoznaj się z artykułem [: jak wymagać zatwierdzonych aplikacji klienckich do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](app-based-conditional-access.md) na potrzeby przykładów konfiguracyjnych.

### <a name="require-app-protection-policy"></a>Wymaganie zasad ochrony aplikacji

W zasadach dostępu warunkowego można wymagać, aby [Zasady ochrony aplikacji usługi Intune](/intune/app-protection-policy) były obecne w aplikacji klienckiej przed udostępnieniem dostępu do wybranych aplikacji w chmurze. 

Aby można było korzystać z tej kontroli dotacji, dostęp warunkowy wymaga zarejestrowania urządzenia w Azure Active Directory, które wymaga użycia aplikacji brokera. Aplikacją brokera jest aplikacja Microsoft Authenticator w przypadku systemu iOS lub aplikacja Portal firmy Microsoft w przypadku urządzeń z systemem Android. Jeśli aplikacja brokera nie jest zainstalowana na urządzeniu podczas próby uwierzytelnienia użytkownika, użytkownik zostanie przekierowany do sklepu z aplikacjami w celu zainstalowania aplikacji brokera.

To ustawienie dotyczy następujących aplikacji klienckich:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Wiele linii dla usługi Intune
- Dziewięć kalendarzy & poczty E-mail

> [!NOTE]
> Programy Microsoft usługi kaizala, Microsoft Skype dla firm i Microsoft Visio nie obsługują uprawnień do **żądania zasad ochrony aplikacji** . Jeśli te aplikacje są wymagane do działania, użyj wyłącznie **zatwierdzonych aplikacji** . Użycie klauzuli or między dwoma dotacjami nie będzie działało dla tych trzech aplikacji.

**Uwagi**

- Aplikacje dla zasad ochrony aplikacji obsługują funkcję zarządzania aplikacjami mobilnymi w usłudze Intune z ochroną zasad.
- Wymagania dotyczące wymagań **zasad ochrony aplikacji** :
    - Obsługuje tylko warunek platformy dla systemów iOS i Android.
    - Aby zarejestrować urządzenie, wymagana jest aplikacja brokera. W systemie iOS aplikacja brokera jest Microsoft Authenticator i w systemie Android jest Intune — Portal firmy App.

Zapoznaj się z artykułem [instrukcje: wymaganie zasad ochrony aplikacji oraz zatwierdzonej aplikacji klienckiej do uzyskiwania dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](app-protection-based-conditional-access.md) na potrzeby przykładów konfiguracyjnych.

### <a name="require-password-change"></a>Wymagaj zmiany hasła 

W przypadku wykrycia ryzyka użytkownika przy użyciu warunków zasad ryzyka użytkownika Administratorzy mogą wybrać, czy użytkownik może bezpiecznie zmienić hasło przy użyciu funkcji samoobsługowego resetowania hasła w usłudze Azure AD. W przypadku wykrycia ryzyka użytkownika użytkownicy mogą przeprowadzić Samoobsługowe resetowanie haseł w celu samodzielnego korygowania hasła. spowoduje to zamknięcie zdarzenia ryzyka użytkownika w celu uniemożliwienia niepotrzebnego hałasu dla administratorów. 

Gdy użytkownik zostanie poproszony o zmianę hasła, najpierw będzie wymagane do ukończenia uwierzytelniania wieloskładnikowego. Upewnij się, że wszyscy użytkownicy zostali zarejestrowani do uwierzytelniania wieloskładnikowego, więc są przygotowani w przypadku wykrycia ryzyka dla konta.  

> [!WARNING]
> Przed wyzwoleniem zasad ryzyka dla użytkowników należy wcześniej zarejestrować się do samoobsługowego resetowania hasła. 

Podczas konfigurowania zasad przy użyciu kontrolki zmiany hasła istnieje ograniczenie kilku miejsc.  

1. Zasady muszą być przypisane do usługi "wszystkie aplikacje w chmurze". Uniemożliwia to osobie atakującej korzystanie z innej aplikacji w celu zmiany hasła użytkownika i zresetowanie ryzyka związanego z kontem, wystarczy zalogować się do innej aplikacji. 
1. Wymaganie zmiany hasła nie może być używane z innymi kontrolkami, takimi jak wymaganie zgodnego urządzenia.  
1. Kontrolki zmiany hasła można używać tylko z warunkiem przypisania użytkownika i grupy, warunku przypisania aplikacji w chmurze (musi to być ustawienie wszystkie) i warunki ryzyka użytkownika. 

### <a name="terms-of-use"></a>Warunki użytkowania

Jeśli Twoja organizacja utworzyła warunki użytkowania, dodatkowe opcje mogą być widoczne w obszarze Udziel kontroli. Te opcje umożliwiają administratorom wymaganie potwierdzenia warunków użytkowania jako stanu dostępu do zasobów chronionych przez zasady. Więcej informacji na temat warunków użytkowania można znaleźć w artykule [Azure Active Directory warunki użytkowania](terms-of-use.md).

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: kontrolki sesji](concept-conditional-access-session.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

- [Tryb samego raportu](concept-conditional-access-report-only.md)
