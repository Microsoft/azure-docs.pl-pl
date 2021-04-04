---
title: Przegląd weryfikacji wydawcy — platforma tożsamości firmy Microsoft | Azure
description: Zawiera omówienie programu weryfikacji wydawcy dla platformy tożsamości firmy Microsoft. Zawiera listę korzyści, wymagań programu i często zadawanych pytań. Gdy aplikacja zostanie oznaczona jako zweryfikowana przez wydawcę, oznacza to, że Wydawca zweryfikował swoją tożsamość przy użyciu konta Microsoft Partner Network, które ukończyło proces weryfikacji i skojarzył to konto MPN z rejestracją aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 1e913e3a5356ad7f49d8b3066f5bd3da7eddd2c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93308769"
---
# <a name="publisher-verification"></a>Weryfikacja wydawcy

Weryfikacja wydawcy pomaga administratorom i użytkownikom końcowym zrozumieć autentyczność deweloperów aplikacji, którzy integrują się z platformą tożsamości firmy Microsoft. 

> [!VIDEO https://www.youtube.com/embed/IYRN2jDl5dc]

Gdy aplikacja zostanie oznaczona jako zweryfikowana przez wydawcę, oznacza to, że Wydawca zweryfikował swoją tożsamość przy użyciu konta [Microsoft Partner Network](https://partner.microsoft.com/membership) , które ukończyło proces [weryfikacji](/partner-center/verification-responses) i skojarzył to konto MPN z rejestracją aplikacji. 

Niebieski wskaźnik "sprawdzony" pojawia się w monicie o zgodę usługi Azure AD i innych ekranach: ![ monit o zgodę](./media/publisher-verification-overview/consent-prompt.png)

Ta funkcja jest przeznaczony głównie dla deweloperów tworzących aplikacje z wieloma dzierżawcami, które korzystają z protokołu [OAuth 2,0 i OpenID Connect](active-directory-v2-protocols.md) z [platformą tożsamości firmy Microsoft](v2-overview.md). Te aplikacje mogą logować użytkowników przy użyciu usługi OpenID Connect Connect lub mogą używać protokołu OAuth 2,0 do żądania dostępu do danych przy użyciu interfejsów API, takich jak [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Korzyści
Weryfikacja wydawcy zapewnia następujące korzyści:
- **Zwiększone przejrzystości i zmniejszanie ryzyka dla klientów**— ta funkcja pomaga klientom zrozumieć, które aplikacje są używane w organizacjach, są publikowane przez deweloperów, którym ufają. 

- **Ulepszona znakowanie**— wskaźnik "zweryfikowany" pojawia się na stronie [monitu o zgodę](application-consent-experience.md)usługi Azure AD, witrynie aplikacje dla przedsiębiorstw i dodatkowych powierzchniach środowiska użytkownika używanych przez użytkowników końcowych i administratorów. 

- **Bezproblemowe wdrażanie w przedsiębiorstwie**— Administratorzy mogą konfigurować [zasady zgody użytkowników](../manage-apps/configure-user-consent.md), używając stanu weryfikacji wydawcy jako jednego z podstawowych kryteriów zasad.

> [!NOTE]
> Począwszy od listopada 2020, użytkownicy końcowi nie będą już mogli przyznawać zgody na większość nowo zarejestrowanych aplikacji wielodostępnych bez zweryfikowanych wydawców. Dotyczy to aplikacji, które są zarejestrowane po 8 listopada 2020, za pomocą protokołu OAuth 2.0 można zażądać uprawnień poza podstawową rejestracją i odczytać profil użytkownika, a następnie poprosić o zgodę użytkowników w różnych dzierżawców niż ta, w której jest zarejestrowana aplikacja. Na ekranie zgody zostanie wyświetlone ostrzeżenie z informacją o tym, że te aplikacje są ryzykowne i pochodzą z niezweryfikowanych wydawców.    

## <a name="requirements"></a>Wymagania
Istnieje kilka wymagań wstępnych dotyczących weryfikacji wydawcy, z których część zostanie już ukończona przez wielu partnerów firmy Microsoft. Są to: 

-  IDENTYFIKATOR MPN dla prawidłowego konta [Microsoft Partner Network](https://partner.microsoft.com/membership) , które ukończyło proces [weryfikacji](/partner-center/verification-responses) . To konto MPN musi być [kontem globalnym partnera (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) dla Twojej organizacji. 

-  Aplikacja zarejestrowana w dzierżawie usługi Azure AD z skonfigurowaną [domeną wydawcy](howto-configure-publisher-domain.md) .

-  Domena adresu e-mail używanego podczas weryfikacji konta MPN musi być zgodna z domeną wydawcy skonfigurowaną w aplikacji lub [domeną niestandardową](../fundamentals/add-custom-domain.md) zweryfikowaną przez system DNS dodaną do dzierżawy usługi Azure AD. 

-  Użytkownik wykonujący weryfikację musi mieć autoryzację, aby wprowadzić zmiany w rejestracji aplikacji w usłudze Azure AD i koncie MPN w centrum partnerskim. 

    -  W usłudze Azure AD ten użytkownik musi być członkiem jednej z następujących [ról](../roles/permissions-reference.md): Administrator aplikacji, administrator aplikacji w chmurze lub Administrator globalny. 

    -  W centrum partnerskim ten użytkownik musi mieć następujące [role](/partner-center/permissions-overview): Administrator MPN, administrator kont lub Administrator globalny (jest to rola udostępniona w usłudze Azure AD).
    
-  Użytkownik wykonujący weryfikację musi zalogować się przy użyciu [uwierzytelniania wieloskładnikowego](../authentication/howto-mfa-getstarted.md).

-  Wydawca wyrazi zgodę na [platformę tożsamości firmy Microsoft dla deweloperów warunki użytkowania](/legal/microsoft-identity-platform/terms-of-use).

Deweloperzy, którzy już spełnili te wymagania wstępne, mogą mieć możliwość zweryfikowania w ciągu kilku minut. Jeśli wymagania nie zostały spełnione, pobieranie konfiguracji jest bezpłatne. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania 
Poniżej przedstawiono kilka często zadawanych pytań dotyczących programu weryfikacji wydawcy. Aby poznać często zadawane pytania dotyczące wymagań i procesu, zobacz [oznaczanie aplikacji jako zweryfikowanej przez wydawcę](mark-app-as-publisher-verified.md).

- **Jakie informacje __nie__ zapewniają weryfikacji wydawcy?**  Gdy aplikacja jest oznaczona jako wydawca zweryfikowana, to nie wskazuje, czy aplikacja lub jej Wydawca osiągnął określone certyfikaty, są zgodne ze standardami branżowymi, zgodne z najlepszymi rozwiązaniami itp. Inne programy firmy Microsoft udostępniają te informacje, w tym [Microsoft 365 certyfikacji aplikacji](/microsoft-365-app-certification/overview).

- **Ile kosztuje ten koszt? Czy wymaga licencji?** Firma Microsoft nie nalicza opłat dla deweloperów na potrzeby weryfikacji wydawcy i nie wymaga żadnej konkretnej licencji. 

- **Jak to odnosi się do Microsoft 365 zaświadczania wydawcy? Co z certyfikatem Microsoft 365 aplikacji?** Są to uzupełniające programy, za pomocą których deweloperzy mogą tworzyć wiarygodne aplikacje, które mogą być bezpiecznie przyjęte przez klientów. Weryfikacja wydawcy to pierwszy krok w tym procesie i powinien zostać wykonany przez wszystkich deweloperów tworzących aplikacje spełniające powyższe kryteria. 

  Deweloperzy, którzy mają integrację z usługą Microsoft 365, mogą uzyskać dodatkowe korzyści z tych programów. Aby uzyskać więcej informacji, zobacz [zaświadczanie wydawcy Microsoft 365](/microsoft-365-app-certification/docs/attestation) i [certyfikat Microsoft 365 aplikacji](/microsoft-365-app-certification/docs/certification). 

- **Czy ta funkcja jest taka sama jak Galeria aplikacji usługi Azure AD?** Nie — weryfikacja wydawcy to komplementarny, ale oddzielny program do [galerii aplikacji Azure Active Directory](v2-howto-app-gallery-listing.md). Deweloperzy, którzy spełniają powyższe kryteria, powinni zakończyć proces weryfikacji wydawcy niezależnie od udziału w tym programie. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się [, jak oznaczyć aplikację jako zweryfikowaną przez wydawcę](mark-app-as-publisher-verified.md).
* [Rozwiązywanie problemów z](troubleshoot-publisher-verification.md) weryfikacją wydawcy.
