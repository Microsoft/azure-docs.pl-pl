---
title: Rejestracja i logowanie za pomocą zasad niestandardowych (wersja zapoznawcza)
titleSuffix: Azure AD B2C
description: Wysyłać hasła jednorazowe (OTP) w wiadomościach tekstowych do telefonów użytkowników aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183962"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Skonfiguruj konto i zaloguj się przy użyciu zasad niestandardowych w Azure AD B2C (wersja zapoznawcza)

Rejestracja i logowanie za pomocą telefonu w Azure Active Directory B2C (Azure AD B2C) umożliwia użytkownikom rejestrowanie się i logowanie do aplikacji przy użyciu hasła jednorazowego (OTP) wysyłanego w wiadomości tekstowej do telefonu. Hasła jednorazowe mogą pomóc zminimalizować ryzyko naruszenia lub naruszania haseł przez użytkowników.

Wykonaj kroki opisane w tym artykule, aby użyć zasad niestandardowych, aby umożliwić klientom rejestrowanie się i logowanie się do aplikacji przy użyciu hasła jednorazowego wysyłanego do telefonu.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Cennik

Hasła jednorazowe są wysyłane do użytkowników za pomocą wiadomości SMS i mogą być naliczone za każdy wysłany komunikat. Aby uzyskać informacje o cenach, zapoznaj się z sekcją **oddzielne opłaty** w [cenniku Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem uwierzytelniania OTP wymagane są następujące zasoby.

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja sieci Web zarejestrowana](tutorial-register-applications.md) w dzierżawie
* [Zasady niestandardowe](custom-policy-get-started.md) przekazane do dzierżawy

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Pobierz konto startowe & logowania za pomocą telefonu

Zacznij od aktualizacji niestandardowych plików zasad rejestracji i logowania na telefonie, aby współpracowały z dzierżawą Azure AD B2C.

W poniższych krokach przyjęto założenie, że zostały spełnione [wymagania wstępne](#prerequisites) i zostało już Sklonowane repozytorium [zasad niestandardowych][starter-pack] na komputerze lokalnym.

1. Znajdź [pliki zasad niestandardowych rejestracji i logowania na telefonie][starter-pack-phone] w lokalnym klonie repozytorium pakietu początkowego lub Pobierz je bezpośrednio. Pliki zasad XML znajdują się w następującym katalogu:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. W każdym pliku Zastąp ciąg `yourtenant` nazwą dzierżawy Azure AD B2C. Na przykład jeśli nazwa dzierżawy usługi B2C jest *contosob2c*, wszystkie wystąpienia `yourtenant.onmicrosoft.com` stają się `contosob2c.onmicrosoft.com`dostępne.

1. Wykonaj kroki opisane w sekcji [Dodawanie identyfikatorów aplikacji do zasad niestandardowych](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [w temacie Rozpoczynanie pracy z zasadami niestandardowymi w Azure Active Directory B2C](custom-policy-get-started.md). W takim przypadku należy zaktualizować `/phone-number-passwordless/` **`Phone_Email_Base.xml`** za pomocą **identyfikatorów aplikacji (klienta)** dwóch aplikacji zarejestrowanych podczas kończenia wymagań wstępnych, *IdentityExperienceFramework* i *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Przekazywanie plików zasad

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do dzierżawy Azure AD B2C.
1. W obszarze **zasady**wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. Przekaż pliki zasad w następującej kolejności:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

Podczas przekazywania każdego pliku, platforma Azure dodaje prefiks `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. W obszarze **zasady niestandardowe**wybierz pozycję **B2C_1A_SignUpOrSignInWithPhone**.
1. W obszarze **Wybierz aplikację**wybierz aplikację *webapp1* , która została zarejestrowana przy wypełnianiu wymagań wstępnych.
1. Dla **opcji wybierz adres URL odpowiedzi**wybierz opcję `https://jwt.ms`.
1. Wybierz pozycję **Uruchom teraz** i zarejestruj się przy użyciu adresu e-mail lub numeru telefonu.
1. Wybierz pozycję **Uruchom teraz** ponownie i zaloguj się przy użyciu tego samego konta, aby upewnić się, że konfiguracja jest poprawna.

## <a name="get-user-account-by-phone-number"></a>Pobierz konto użytkownika według numeru telefonu

Użytkownik, który zarejestruje się przy użyciu numeru telefonu, ale nie poda adresu e-mail odzyskiwania, jest rejestrowany w katalogu Azure AD B2C przy użyciu numeru telefonu jako nazwy logowania. Jeśli użytkownik chce zmienić swój numer telefonu, dział pomocy technicznej lub zespół pomocy technicznej musi najpierw znaleźć swoje konto, a następnie zaktualizować numer telefonu.

Możesz znaleźć użytkownika według numeru telefonu (nazwy logowania), korzystając z [Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Przykład:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Następne kroki

W witrynie GitHub możesz znaleźć pakiet startowy zasad dotyczących rejestracji i logowania na telefonie.

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/scenariuszach/numer telefonu — bezhasło][starter-pack-phone]

Pliki zasad pakietu startowego używają profilów technicznych usługi uwierzytelniania wieloskładnikowego i przekształceń numerów telefonów:

* [Zdefiniuj profil techniczny usługi Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md)
* [Definiowanie przekształceń oświadczeń numeru telefonu](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
