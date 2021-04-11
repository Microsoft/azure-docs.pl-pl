---
title: Logowanie za klucz zabezpieczeń bezhasło-Azure Active Directory
description: Włącz logowanie za pomocą klucza zabezpieczeń bezhasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8779ee2d3c4d83c06a2c1803e65219d43f0ef14
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275808"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Włącz logowanie przy użyciu klucza zabezpieczeń bezhasło 

W przypadku przedsiębiorstw korzystających dzisiaj z haseł i mających udostępnione środowisko komputera klucze zabezpieczeń umożliwiają bezproblemowe uwierzytelnianie procesów roboczych bez wprowadzania nazwy użytkownika lub hasła. Klucze zabezpieczeń zapewniają lepszą produktywność dla pracowników i mają lepsze zabezpieczenia.

Ten dokument koncentruje się na włączaniu uwierzytelniania bezhaseł opartego na kluczu zabezpieczeń. Na końcu tego artykułu będzie można zalogować się do aplikacji sieci Web za pomocą konta usługi Azure AD przy użyciu klucza zabezpieczeń FIDO2.

## <a name="requirements"></a>Wymagania

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Włącz [rejestrację informacji o zabezpieczeniach połączonych](concept-registration-mfa-sspr-combined.md)
- Zgodne [FIDO2 klucze zabezpieczeń](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN wymaga systemu Windows 10 w wersji 1903 lub nowszej * *

Aby używać kluczy zabezpieczeń do logowania się do usługi Web Apps i usług, musisz mieć przeglądarkę obsługującą protokół WebAuthN. Obejmują one programy Microsoft Edge, Chrome, Firefox i Safari.

## <a name="prepare-devices"></a>Przygotowywanie urządzeń

W przypadku urządzeń przyłączonych do usługi Azure AD najlepszym rozwiązaniem jest system Windows 10 w wersji 1903 lub nowszej.

Urządzenia dołączone do hybrydowej usługi Azure AD muszą korzystać z systemu Windows 10 w wersji 2004 lub nowszej.

## <a name="enable-passwordless-authentication-method"></a>Włącz metodę uwierzytelniania bezhasła

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji dla metod uwierzytelniania bezhaseł polegają na funkcji rejestracji połączonej. Wykonaj kroki opisane w artykule [Włączanie rejestracji informacji o zabezpieczeniach połączonych](howto-registration-mfa-sspr-combined.md), aby włączyć rejestrację łączną.

### <a name="enable-fido2-security-key-method"></a>Włącz metodę klucza zabezpieczeń FIDO2

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory**  >    >  **metodami** uwierzytelniania zabezpieczeń metody  >  **uwierzytelniania**.
1. W obszarze **klucz zabezpieczeń metody FIDO2** wybierz następujące opcje:
   1. **Włącz** — tak lub nie
   1. **Cel** — wszyscy użytkownicy lub wybrani użytkownicy
1. **Zapisz** konfigurację.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Rejestracja użytkownika i zarządzanie kluczami zabezpieczeń FIDO2

1. Przejdź do [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Zaloguj się, jeśli jeszcze nie zostało to zrobione.
1. Kliknij pozycję **informacje zabezpieczające**.
   1. Jeśli użytkownik ma już zarejestrowaną co najmniej jedną metodę Multi-Factor Authentication usługi Azure AD, może natychmiast zarejestrować klucz zabezpieczeń FIDO2.
   1. Jeśli nie zarejestrowano co najmniej jednej metody Multi-Factor Authentication usługi Azure AD, należy dodać ją.
1. Dodaj klucz zabezpieczeń FIDO2, klikając pozycję **Dodaj metodę** i wybierając pozycję **klucz zabezpieczeń**.
1. Wybierz **urządzenie USB** lub **urządzenie NFC**.
1. Przygotuj swój klucz i wybierz pozycję **dalej**.
1. Zostanie wyświetlone pole i poproszenie użytkownika o utworzenie/wprowadzenie numeru PIN dla klucza zabezpieczeń, a następnie przeprowadzenie wymaganego gestu dla klucza, biometrycznego lub dotykowego.
1. Użytkownik zostanie odesłany do połączonego środowiska rejestracji i zostanie poproszony o podanie zrozumiałej nazwy klucza, aby użytkownik mógł określić, który z nich ma wiele. Kliknij przycisk **Dalej**.
1. Kliknij pozycję **gotowe** , aby zakończyć proces.

## <a name="sign-in-with-passwordless-credential"></a>Zaloguj się przy użyciu poświadczeń bez hasła

W przykładzie poniżej Użytkownik zainicjowano już swój klucz zabezpieczeń FIDO2. Użytkownik może zdecydować się na zalogowanie się w sieci Web przy użyciu klucza zabezpieczeń FIDO2 w obsługiwanej przeglądarce w systemie Windows 10 w wersji 1903 lub nowszej.

![Logowanie do klucza zabezpieczeń w przeglądarce Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkać problemy związane z tą funkcją, Udostępnij za pośrednictwem aplikacji centrum opinii o systemie Windows, wykonując następujące czynności:

1. Uruchom **centrum opinii** i upewnij się, że użytkownik jest zalogowany.
1. Prześlij opinię poniżej następującej kategoryzacji:
   - Kategoria: zabezpieczenia i prywatność
   - Podkategoria: FIDO
1. Aby przechwytywać dzienniki, użyj opcji w celu **ponownego utworzenia mojego problemu**.

## <a name="known-issues"></a>Znane problemy

### <a name="security-key-provisioning"></a>Inicjowanie obsługi kluczy zabezpieczeń

Inicjowanie obsługi administracyjnej kluczy zabezpieczeń przez administratora i niedostępność nie jest możliwe.

**Uwaga:** Logowanie w pamięci podręcznej FIDO2 nie powiedzie się na komputerze przyłączonym do hybrydowej usługi Azure AD specyficznym dla wersji Win10 20H2 (gdy LOS do kontrolera domeny Jest to obecnie badane z pracą inżynieryjną.

### <a name="upn-changes"></a>Zmiany nazwy UPN

Pracujemy nad obsługą funkcji, która umożliwia zmianę nazwy UPN na przyłączonych do mnie hybrydowych urządzeniach usługi Azure AD i przyłączonych do usługi Azure AD. W przypadku zmiany nazwy UPN użytkownika nie można już modyfikować kluczy zabezpieczeń FIDO2, aby uwzględnić zmianę. Rozwiązanie polega na zresetowaniu urządzenia i ponownym zarejestrowaniu użytkownika.

## <a name="next-steps"></a>Następne kroki

[Logowanie do klucza zabezpieczeń FIDO2 systemu Windows 10](howto-authentication-passwordless-security-key-windows.md)

[Włączanie uwierzytelniania FIDO2 w zasobach lokalnych](howto-authentication-passwordless-security-key-on-premises.md)

[Dowiedz się więcej o rejestrowaniu urządzeń](../devices/overview.md)

[Dowiedz się więcej o usłudze Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
