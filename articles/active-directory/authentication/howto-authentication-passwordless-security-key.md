---
title: Logowanie za pomocą klucza zabezpieczeń bez hasła — Azure Active Directory
description: Włączanie logowania bez hasła klucza zabezpieczeń do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436a972693aafd220d277d7411c0da12636e9cc6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829804"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Włączanie logowania za pomocą klucza zabezpieczeń bez hasła 

W przypadku przedsiębiorstw, które obecnie używają haseł i mają udostępnione środowisko komputera, klucze zabezpieczeń zapewniają pracownikom bezproblemowe uwierzytelnianie bez wprowadzania nazwy użytkownika lub hasła. Klucze zabezpieczeń zapewniają lepszą produktywność pracowników i zapewniają lepsze zabezpieczenia.

Ten dokument koncentruje się na włączaniu uwierzytelniania bez hasła opartego na kluczach zabezpieczeń. Na końcu tego artykułu będziesz w stanie zalogować się do aplikacji internetowych przy użyciu konta usługi Azure AD przy użyciu klucza zabezpieczeń FIDO2.

## <a name="requirements"></a>Wymagania

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Włączanie [rejestracji połączonych informacji zabezpieczających](concept-registration-mfa-sspr-combined.md)
- Zgodne [klucze zabezpieczeń FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN wymaga Windows 10 wersji 1903 lub wyższej**

Aby używać kluczy zabezpieczeń do logowania się do aplikacji i usług internetowych, musisz mieć przeglądarkę, która obsługuje protokół WebAuthN. Należą do Microsoft Edge, Chrome, Firefox i Safari.


## <a name="prepare-devices"></a>Przygotowywanie urządzeń

W przypadku urządzeń przyłączonych do usługi Azure AD najlepsze środowisko jest Windows 10 wersji 1903 lub wyższej.

Urządzenia przyłączone do hybrydowej usługi Azure AD muszą Windows 10 w wersji 2004 lub wyższej.

## <a name="enable-passwordless-authentication-method"></a>Włączanie metody uwierzytelniania bez hasła

### <a name="enable-the-combined-registration-experience"></a>Włączanie połączonego procesu rejestracji

Funkcje rejestracji dla metod uwierzytelniania bez hasła korzystają z połączonej funkcji rejestracji. Wykonaj kroki opisane w artykule Włączanie rejestracji [połączonych informacji zabezpieczających](howto-registration-mfa-sspr-combined.md), aby włączyć rejestrację połączoną.

### <a name="enable-fido2-security-key-method"></a>Włączanie metody klucza zabezpieczeń FIDO2

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź **do** Azure Active Directory  >  **Metody**  >  **uwierzytelniania zabezpieczeń Zasady** metody  >  **uwierzytelniania.**
1. W obszarze klucza **zabezpieczeń FIDO2 metody** wybierz następujące opcje:
   1. **Włącz** — Tak lub Nie
   1. **Cel** — wszyscy użytkownicy lub Wybierz użytkowników
1. **Zapisz** konfigurację.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Rejestracja użytkownika i zarządzanie kluczami zabezpieczeń FIDO2

1. Przejdź do [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Zaloguj się, jeśli jeszcze nie zostało to wcześniej.
1. Kliknij **pozycję Informacje zabezpieczające.**
   1. Jeśli użytkownik ma już zarejestrowaną co najmniej jedną metodę usługi Azure AD Multi-Factor Authentication, może natychmiast zarejestrować klucz zabezpieczeń FIDO2.
   1. Jeśli nie ma zarejestrowanej co najmniej jednej metody usługi Azure AD Multi-Factor Authentication, należy ją dodać.
1. Dodaj klucz zabezpieczeń FIDO2, klikając pozycję **Dodaj metodę** i wybierając **pozycję Klucz zabezpieczeń**.
1. Wybierz **urządzenie USB lub** urządzenie **NFC.**
1. Przygotuj klucz i wybierz pozycję **Dalej.**
1. Zostanie wyświetlone pole z prośbą o utworzenie/wprowadzenie numeru PIN klucza zabezpieczeń, a następnie wykonanie wymaganego gestu dla klucza ( biometrycznego lub dotykowego).
1. Użytkownik zostanie zwrócony do połączonego procesu rejestracji i zostanie poproszony o podanie znaczącej nazwy klucza, aby użytkownik był w stanie określić, która z nich ma wiele. Kliknij przycisk **Dalej**.
1. Kliknij **przycisk Gotowe,** aby ukończyć proces.

## <a name="sign-in-with-passwordless-credential"></a>Logowanie się przy użyciu poświadczeń bez hasła

W poniższym przykładzie użytkownik aprowizował już klucz zabezpieczeń FIDO2. Użytkownik może zalogować się w Internecie przy użyciu klucza zabezpieczeń FIDO2 w obsługiwanej przeglądarce w Windows 10 wersji 1903 lub wyższej.

![Zasady logowania przy Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz podzielić się opinią lub napotkać problemy z tą funkcją, udostępnij za pośrednictwem Centrum opinii o systemie Windows, korzystając z następujących czynności:

1. Uruchom **Centrum opinii** i upewnij się, że się zalogowano.
1. Prześlij opinię w ramach następującej kategoryzacji:
   - Kategoria: Bezpieczeństwo i prywatność
   - Podkategoria: FIDO
1. Aby przechwycić dzienniki, użyj opcji **Utwórz ponownie mój problem.**

## <a name="known-issues"></a>Znane problemy

### <a name="security-key-provisioning"></a>Aprowizowanie kluczy zabezpieczeń

Aprowizowanie i cokaprowizowanie kluczy zabezpieczeń przez administratora nie jest dostępne.

### <a name="cached-logon-on-hybrid-azure-ad-joined-devices"></a>Logowanie z pamięci podręcznej na urządzeniach przyłączony do hybrydowej usługi Azure AD

Logowanie z pamięci podręcznej przy użyciu kluczy FIDO2 kończy się niepowodzeniem na urządzeniach przyłączonych do hybrydowej usługi Azure AD Windows 10 wersji 20H2. W związku z tym użytkownicy nie będą mogli zalogować się, gdy dostęp do lokalnego kontrolera domeny będzie niedostępny. Obecnie trwa badanie tej czynności.

### <a name="upn-changes"></a>Zmiany nazwy UPN

Pracujemy nad obsługą funkcji, która umożliwia zmianę upn na urządzeniach przyłączony do hybrydowej usługi Azure AD i przyłączony do usługi Azure AD. Jeśli nazwa UPN użytkownika zmieni się, nie można już modyfikować kluczy zabezpieczeń FIDO2 w celu uwzględnienia zmiany. Rozwiązanie to zresetowanie urządzenia i ponowne zarejestrowanie użytkownika.

## <a name="next-steps"></a>Następne kroki

[Klucz zabezpieczeń FIDO2 Windows 10 logowania](howto-authentication-passwordless-security-key-windows.md)

[Włączanie uwierzytelniania FIDO2 w zasobach lokalnych](howto-authentication-passwordless-security-key-on-premises.md)

[Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md)

[Dowiedz się więcej o usłudze Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
