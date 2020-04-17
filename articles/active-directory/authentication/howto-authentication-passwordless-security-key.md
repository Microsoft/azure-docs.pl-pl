---
title: Logowanie się bez hasła (wersja zapoznawcza) — usługa Azure Active Directory
description: Włączanie logowania się bez użycia hasła do usługi Azure AD przy użyciu kluczy zabezpieczeń FIDO2 (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066ab7892bed6e7505e7ee114ff37a7850ef5c9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450925"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Włączanie logowania bez hasła (wersja zapoznawcza)

W przypadku przedsiębiorstw, które używają haseł już dziś i mają wspólne środowisko komputera, klucze zabezpieczeń zapewniają pracownikom bezproblemowy sposób uwierzytelniania bez wprowadzania nazwy użytkownika lub hasła. Klucze zabezpieczeń zapewniają pracownikom większą produktywność i zapewniają lepsze bezpieczeństwo.

Ten dokument koncentruje się na włączaniu uwierzytelniania bez hasła opartego na kluczach zabezpieczeń. Na końcu tego artykułu będzie można zalogować się do aplikacji opartych na sieci Web za pomocą konta usługi Azure AD przy użyciu klucza zabezpieczeń FIDO2.

|     |
| --- |
| Klucze zabezpieczeń FIDO2 są publiczną funkcją podglądu usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Wymagania

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Połączona wersja zapoznawcza rejestracji informacji o zabezpieczeniach](concept-registration-mfa-sspr-combined.md)
- Kompatybilne [klucze zabezpieczeń FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Sieć WebAuthN wymaga systemu Windows 10 w wersji 1809 lub nowszej**

Aby używać kluczy zabezpieczeń do logowania się do aplikacji i usług sieci Web, musisz mieć przeglądarkę obsługującą protokół WebAuthN. Należą do nich Microsoft Edge, Chrome, Firefox i Safari.

## <a name="prepare-devices-for-preview"></a>Przygotowywanie urządzeń do podglądu

Urządzenia przyłączone do usługi Azure AD, z którymi pilotujesz, muszą działać w systemie Windows 10 w wersji 1809 lub nowszej. Najlepsze środowisko jest w systemie Windows 10 w wersji 1903 lub nowszej.

Hybrydowe urządzenia przyłączone do usługi Azure AD muszą działać w kompilacji niejawnego programu testów systemu Windows 10 18945 lub nowszej.

## <a name="enable-passwordless-authentication-method"></a>Włącz metodę uwierzytelniania bez hasła

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji metod uwierzytelniania bez hasła opierają się na połączonej funkcji rejestracji. Postępuj zgodnie z instrukcjami zawartymi w artykule [Włączanie połączonej rejestracji informacji o zabezpieczeniach (wersja zapoznawcza)](howto-registration-mfa-sspr-combined.md), aby włączyć rejestrację połączoną.

### <a name="enable-fido2-security-key-method"></a>Włącz metodę klucza zabezpieczeń FIDO2

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do zasad**metody uwierzytelniania** > **zabezpieczeń** >  **usługi Azure Active Directory** > **Zasady metody uwierzytelniania (Wersja zapoznawcza)**.
1. W obszarze metoda **KLUCZ ZABEZPIECZEŃ FIDO2**wybierz następujące opcje:
   1. **Włącz** — Tak lub Nie
   1. **Cel** — wszyscy użytkownicy lub wybierz użytkowników
1. **Zapisz** konfigurację.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Rejestracja i zarządzanie kluczami bezpieczeństwa FIDO2 przez użytkownika

1. Przejdź do [https://myprofile.microsoft.com](https://myprofile.microsoft.com)pliku .
1. Zaloguj się, jeśli jeszcze nie.
1. Kliknij pozycję **Informacje zabezpieczające**.
   1. Jeśli użytkownik ma już co najmniej jedną zarejestrowaną metodę uwierzytelniania wieloskładnikowego platformy Azure, może natychmiast zarejestrować klucz zabezpieczeń FIDO2.
   1. Jeśli nie mają co najmniej jednej metody uwierzytelniania wieloskładnikowego platformy Azure, muszą dodać jedną.
1. Dodaj klucz zabezpieczeń FIDO2, klikając pozycję **Dodaj metodę** i wybierając pozycję **Klawisz zabezpieczeń**.
1. Wybierz **urządzenie USB** lub urządzenie **NFC**.
1. Przygotuj klucz i wybierz pozycję **Dalej**.
1. Pojawi się okno i poprosi użytkownika o utworzenie/wprowadzenie kodu PIN dla klucza zabezpieczeń, a następnie wykonanie wymaganego gestu dla klucza, biometrycznego lub dotykowego.
1. Użytkownik zostanie zwrócony do połączonego środowiska rejestracji i poproszony o podanie znaczącej nazwy klucza, dzięki czemu użytkownik może zidentyfikować, który z nich ma wiele. Kliknij przycisk **Dalej**.
1. Kliknij **przycisk Gotowe,** aby zakończyć proces.

## <a name="sign-in-with-passwordless-credential"></a>Logowanie się przy użyciu poświadczeń bez hasła

W poniższym przykładzie użytkownik aprowizować już swój klucz zabezpieczeń FIDO2. Użytkownik może zalogować się w internecie za pomocą klucza zabezpieczeń FIDO2 wewnątrz obsługiwanej przeglądarki w systemie Windows 10 w wersji 1809 lub nowszej.

![Logowanie do klucza zabezpieczeń Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Rozwiązywanie problemów i opinie

Jeśli chcesz udostępnić opinię lub napotkasz problemy podczas wyświetlania podglądu tej funkcji, udostępnij za pośrednictwem aplikacji Centrum opinii systemu Windows, wykonując następujące czynności:

1. Uruchom **Centrum opinii** i upewnij się, że się zalogowałeś.
1. Prześlij opinię w następującej kategorii:
   - Kategoria: Bezpieczeństwo i prywatność
   - Podkategoria: FIDO
1. Aby przechwycić dzienniki, użyj **opcji,** aby ponownie utworzyć problem

## <a name="known-issues"></a>Znane problemy

### <a name="security-key-provisioning"></a>Inicjowanie obsługi administracyjnej kluczy zabezpieczeń

Administrator inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej kluczy zabezpieczeń nie jest dostępna w publicznej wersji zapoznawczej.

### <a name="upn-changes"></a>Zmiany nazwy UPN

Pracujemy nad obsługą funkcji, która umożliwia zmianę sieci UPN na hybrydowych urządzeniach przyłączonych do usługi Azure AD i przyłączonych do usługi Azure AD. Jeśli numer UPN użytkownika ulegnie zmianie, nie można już modyfikować kluczy zabezpieczeń FIDO2 w celu uwzględnienia tej zmiany. Rozdzielczość jest zresetować urządzenie i użytkownik musi ponownie zarejestrować.

## <a name="next-steps"></a>Następne kroki

[Klucz zabezpieczeń FIDO2 Windows 10 zaloguj się](howto-authentication-passwordless-security-key-windows.md)

[Włączanie uwierzytelniania FIDO2 w zasobach lokalnych](howto-authentication-passwordless-security-key-on-premises.md)

[Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md)

[Dowiedz się więcej o uwierzytelnianiu wieloskładnikowym platformy Azure](../authentication/howto-mfa-getstarted.md)
