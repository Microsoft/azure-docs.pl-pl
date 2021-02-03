---
title: Zabezpieczanie usługi MFA opartej na telefonie w Azure AD B2C
titleSuffix: Azure AD B2C
description: Zapoznaj się z poradami dotyczącymi zabezpieczania uwierzytelniania wieloskładnikowego (MFA) w dzierżawie Azure AD B2C przy użyciu Azure Monitor Log Analytics raportów i alertów. Skorzystaj z naszego skoroszytu, aby identyfikować fałszywe uwierzytelnienia telefoniczne i ograniczyć fałszywe rejestracje. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca73e020009817001f309ddf29c2984a8541026
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527482"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Zabezpieczanie usługi uwierzytelniania wieloskładnikowego opartego na telefonie (MFA)

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Dzięki usłudze Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) użytkownicy mogą zdecydować się na otrzymywanie zautomatyzowanego połączenia głosowego na numer telefonu, który rejestruje się pod kątem weryfikacji. Złośliwi użytkownicy mogą wykorzystać tę metodę przez utworzenie wielu kont i nawiązanie połączenia telefonicznego bez wykonywania procesu rejestracji usługi MFA. Te liczne nieudane logowania mogą wyczerpać dozwolone próby rejestracji, uniemożliwiając innym użytkownikom rejestrowanie się w celu uzyskania nowych kont w dzierżawie Azure AD B2C. Aby chronić przed atakami, można użyć Azure Monitor do monitorowania niepowodzeń uwierzytelniania telefonu i łagodzenia fałszywych logowań.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem Utwórz [obszar roboczy log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Utwórz skoroszyt zdarzeń MFA oparty na telefonie

Repozytorium [Azure AD B2C raporty & alertów](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) w usłudze GitHub zawiera artefakty, których można użyć do tworzenia i publikowania raportów, alertów i pulpitów nawigacyjnych opartych na dziennikach Azure AD B2C. Skoroszyt roboczy przedstawiony poniżej przedstawia błędy związane z telefonem.

### <a name="overview-tab"></a>Karta przegląd

Na karcie **Omówienie** są wyświetlane następujące informacje:

- Przyczyny niepowodzenia (całkowita liczba nieudanych uwierzytelnień w telefonie dla każdego z przyczyn)
- Zablokowany ze względu na nieprawidłową reputację
- Adres IP z nieudanymi uwierzytelnieniami na telefonie (łączna liczba nieudanych uwierzytelnień w telefonie dla każdego z podanym adresem IP)
- Numery telefonów z niepowodzeniem uwierzytelnianiem w ramach adresów IP
- Przeglądarka (błędy uwierzytelniania telefonu na przeglądarkę klienta)
- System operacyjny (błędy uwierzytelniania telefonu na system operacyjny klienta)

![Karta przegląd](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Karta Szczegóły

Na karcie **szczegóły** są raportowane następujące informacje:

- Zasady Azure AD B2C — uwierzytelnienia na telefonie nie powiodło się
- Błędy uwierzytelniania telefonu według numeru telefonu — wykres czasu (przystawka osi czasu)
- Błędy uwierzytelniania telefonu przez zasady Azure AD B2C — wykres czasu (przystawka osi czasu)
- Błędy uwierzytelniania na telefonie według adresu IP — wykres czasu (przystawka osi czasu)
- Wybierz numer telefonu, aby wyświetlić szczegóły niepowodzenia (Wybierz numer telefonu, aby uzyskać szczegółową listę błędów)

![Karta Szczegóły 1 z 3](media/phone-based-mfa/details-tab-1.png)

![Karta Szczegóły 2 z 3](media/phone-based-mfa/details-tab-2.png)

![Karta Szczegóły 3 z 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Używanie skoroszytu do identyfikowania fałszywych rejestracji

Możesz użyć skoroszytu, aby zrozumieć zdarzenia usługi MFA oparte na telefonie i identyfikować potencjalnie złośliwe użycie usług telefonii.

1. Zapoznaj się z normalnymi informacjami dla dzierżawy, odpowiadając na następujące pytania:

   - Gdzie są regiony, z których oczekujesz usługi MFA opartej na telefonie?
   - Przejrzyj przyczyny niepowodzenia prób MFA opartych na telefonie; Czy są one uznawane za normalne czy oczekiwane?

2. Rozpoznawanie cech fałszywych rejestracji:

   - **Oparte na lokalizacji**: sprawdzanie **niepowodzeń uwierzytelniania telefonu według adresu IP** dla kont skojarzonych z lokalizacjami, z których użytkownicy nie mogą się zarejestrować.

   > [!NOTE]
   > Podany adres IP jest przybliżonym regionem.

   - **Szybkość**: należy szukać nieprawidłowych **uwierzytelnień na telefonie (dziennie)**, co oznacza, że numery telefonów, które wydają nietypową liczbę nieudanych prób uwierzytelnienia na telefonie, są uporządkowane od najwyższego do najniższego (po prawej).

3. Aby wyeliminować oszukańcze rejestrowanie, wykonaj kroki opisane w następnej sekcji.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Eliminowanie fałszywych logowań

Wykonaj poniższe czynności, aby pomóc w ograniczeniu fałszywych logowań.

- Użyj **zalecanych** wersji przepływów użytkowników, aby wykonać następujące czynności:
     
   - [Włącz funkcję jednorazowego kodu dostępu wiadomości e-mail (OTP)](phone-authentication-user-flows.md) dla usługi MFA (dotyczy zarówno przepływów rejestrowania i logowania).
   - [Skonfiguruj zasady dostępu warunkowego](conditional-access-identity-protection-setup.md) , aby blokować logowania na podstawie lokalizacji (dotyczy tylko przepływów logowania, a nie do przepływów).
   - Za pomocą łączników interfejsu API można [zintegrować z rozwiązaniem bot, takim jak reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (dotyczy przepływów tworzenia konta).

- Usuń kody krajów, które nie są istotne dla Twojej organizacji, w menu rozwijanym, w którym użytkownik weryfikuje numer telefonu (Ta zmiana zostanie zastosowana do przyszłych logowań):
    
   1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD B2C.

   2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.

   3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.

   4. Wybierz przepływ użytkownika, a następnie wybierz pozycję **Języki**. Wybierz język lokalizacji geograficznej organizacji, aby otworzyć panel szczegóły języka. (W tym przykładzie wybierzemy **angielski EN** dla Stany Zjednoczone). Wybierz **stronę uwierzytelnianie wieloskładnikowe**, a następnie wybierz pozycję **Pobierz ustawienia domyślne (EN)**.
 
      ![Przekaż nowe zastąpienia, aby pobrać wartości domyślne](media/phone-based-mfa/download-defaults.png)

   5. Otwórz plik JSON, który został pobrany w poprzednim kroku. W pliku Wyszukaj ciąg `DEFAULT` i zastąp go wierszem `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Pamiętaj, aby ustawić `Overrides` wartość `true` .

   > [!NOTE]
   > Możesz dostosować listę dozwolonych kodów krajów w `countryList` elemencie (zobacz [przykład na stronie uwierzytelnianie za pomocą współczynnika telefonicznego](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Zapisz plik JSON. W panelu Szczegóły języka w obszarze **Przekaż nowe zastąpienia** wybierz zmodyfikowany plik JSON, aby go załadować.

   8. Zamknij panel i wybierz pozycję **Uruchom przepływ użytkownika**. Na potrzeby tego przykładu upewnij się, że **Stany Zjednoczone** jest jedynym kodem kraju dostępnym na liście rozwijanej:
 
      ![Lista rozwijana z kodem kraju](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Następne kroki

- Informacje [na temat ochrony tożsamości i dostępu warunkowego dla Azure AD B2C](conditional-access-identity-protection-overview.md) 

- Stosowanie [dostępu warunkowego do przepływów użytkowników w Azure Active Directory B2C](conditional-access-user-flow.md)