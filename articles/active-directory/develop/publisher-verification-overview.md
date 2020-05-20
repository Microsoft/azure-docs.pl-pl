---
title: Przegląd weryfikacji wydawcy — platforma tożsamości firmy Microsoft | Azure
description: Zawiera omówienie programu weryfikacji wydawcy (wersja zapoznawcza) dla platformy tożsamości firmy Microsoft. Zawiera listę korzyści, wymagań programu i często zadawanych pytań. Gdy aplikacja zostanie oznaczona jako zweryfikowana przez wydawcę, oznacza to, że Wydawca zweryfikował swoją tożsamość przy użyciu konta Microsoft Partner Network, które ukończyło proces weryfikacji i skojarzył to konto MPN z rejestracją aplikacji.
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
ms.openlocfilehash: 73a96f295d5dfa74130927e5096e9278a0e348e8
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682427"
---
# <a name="publisher-verification-preview"></a>Weryfikacja wydawcy (wersja zapoznawcza)

Weryfikacja wydawcy (wersja zapoznawcza) pomaga administratorom i użytkownikom końcowym zrozumieć autentyczność deweloperów aplikacji integrujących się z platformą tożsamości firmy Microsoft. Gdy aplikacja zostanie oznaczona jako zweryfikowana przez wydawcę, oznacza to, że Wydawca zweryfikował swoją tożsamość przy użyciu konta [Microsoft Partner Network](https://partner.microsoft.com/membership) , które ukończyło proces [weryfikacji](/partner-center/verification-responses) i skojarzył to konto MPN z rejestracją aplikacji. 

Niebieski wskaźnik "sprawdzony" pojawia się w monicie o zgodę usługi Azure AD i innych ekranach: ![ monit o zgodę](./media/publisher-verification-overview/consent-prompt.png)

Ta funkcja jest przeznaczony głównie dla deweloperów tworzących aplikacje z wieloma dzierżawcami, które korzystają z protokołu [OAuth 2,0 i OpenID Connect](active-directory-v2-protocols.md) z [platformą tożsamości firmy Microsoft](v2-overview.md). Te aplikacje mogą logować użytkowników przy użyciu usługi OpenID Connect Connect lub mogą używać protokołu OAuth 2,0 do żądania dostępu do danych przy użyciu interfejsów API, takich jak [Microsoft Graph](https://developer.microsoft.com/graph/).

## <a name="benefits"></a>Korzyści
Weryfikacja wydawcy zapewnia następujące korzyści:
- **Zwiększone przejrzystości i zmniejszanie ryzyka dla klientów**— ta funkcja pomaga klientom zrozumieć, które aplikacje są używane w organizacjach, są publikowane przez deweloperów, którym ufają. 

- **Ulepszona znakowanie**— wskaźnik "zweryfikowany" pojawia się na stronie [monitu o zgodę](application-consent-experience.md)usługi Azure AD, witrynie aplikacje dla przedsiębiorstw i dodatkowych powierzchniach środowiska użytkownika używanych przez użytkowników końcowych i administratorów. 

- **Bezproblemowe wdrażanie w przedsiębiorstwie**— Administratorzy mogą konfigurować nowe zasady zgody użytkowników, a stan weryfikacji wydawcy będzie jednym z podstawowych kryteriów zasad. 

- **Ulepszona Ocena ryzyka**— wykrycia firmy Microsoft dla "ryzykownych" żądań zgody będą obejmować weryfikację wydawcy jako sygnał. 

## <a name="requirements"></a>Wymagania
Istnieje kilka wymagań wstępnych dotyczących weryfikacji wydawcy, z których część zostanie już ukończona przez wielu partnerów firmy Microsoft. Oto one: 

-  IDENTYFIKATOR MPN dla prawidłowego konta [Microsoft Partner Network](https://partner.microsoft.com/membership) , które ukończyło proces [weryfikacji](/partner-center/verification-responses) . To konto MPN musi być [kontem globalnym partnera (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) dla Twojej organizacji. 

-  Dzierżawa usługi Azure AD z [domeną niestandardową](/azure/active-directory/fundamentals/add-custom-domain)zweryfikowaną przez system DNS. Domena niestandardowa musi być zgodna z domeną adresu e-mail używanego podczas weryfikacji w poprzednim kroku. 

-  Aplikacja zarejestrowana w dzierżawie usługi Azure AD z [domeną wydawcy](howto-configure-publisher-domain.md) skonfigurowaną przy użyciu tej samej domeny, która została wcześniej użyta. 

-  Użytkownik wykonujący weryfikację musi mieć autoryzację, aby wprowadzić zmiany w rejestracji aplikacji w usłudze Azure AD i koncie MPN w centrum partnerskim. 

    -  W usłudze Azure AD ten użytkownik musi być właścicielem aplikacji lub mieć jedną z następujących [ról](/azure/active-directory/users-groups-roles/directory-assign-admin-roles): Administrator aplikacji, administrator aplikacji w chmurze, Administrator globalny. 

    -  W centrum partnerskim ten użytkownik musi mieć następujące [role](/partner-center/permissions-overview): Administrator MPN, administrator kont lub Administrator globalny (jest to rola udostępniona w usłudze Azure AD).
    
-  Wydawca wyrazi zgodę na [platformę tożsamości firmy Microsoft dla deweloperów warunki użytkowania](/legal/microsoft-identity-platform/terms-of-use).

Deweloperzy, którzy już spełnili te wymagania wstępne, mogą mieć możliwość zweryfikowania w ciągu kilku minut. Jeśli wymagania nie zostały spełnione, pobieranie konfiguracji jest bezpłatne. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania 
Poniżej przedstawiono kilka często zadawanych pytań dotyczących programu weryfikacji wydawcy. Aby poznać często zadawane pytania dotyczące wymagań i procesu, zobacz [oznaczanie aplikacji jako zweryfikowanej przez wydawcę](mark-app-as-publisher-verified.md).

- **Jakie informacje __nie__ zapewniają weryfikacji wydawcy?**  Gdy aplikacja jest oznaczona jako wydawca zweryfikowana, to nie wskazuje, czy aplikacja lub jej Wydawca osiągnął określone certyfikaty, są zgodne ze standardami branżowymi, zgodne z najlepszymi rozwiązaniami itp. Inne programy firmy Microsoft udostępniają te informacje, w tym [Microsoft 365 certyfikacji aplikacji](/microsoft-365-app-certification/overview).

- **Ile kosztuje ten koszt? Czy wymaga licencji?** Firma Microsoft nie nalicza opłat dla deweloperów na potrzeby weryfikacji wydawcy i nie wymaga żadnej konkretnej licencji. 

- **Jak to odnosi się do Microsoft 365 zaświadczania wydawcy? Co z certyfikatem Microsoft 365 aplikacji?** Są to uzupełniające programy, za pomocą których deweloperzy mogą tworzyć wiarygodne aplikacje, które mogą być bezpiecznie przyjęte przez klientów. Weryfikacja wydawcy to pierwszy krok w tym procesie i powinien zostać wykonany przez wszystkich deweloperów tworzących aplikacje spełniające powyższe kryteria. 

  Deweloperzy, którzy mają integrację z usługą Microsoft 365, mogą uzyskać dodatkowe korzyści z tych programów. Aby uzyskać więcej informacji, zobacz [zaświadczanie wydawcy Microsoft 365](/microsoft-365-app-certification/docs/attestation) i [certyfikat Microsoft 365 aplikacji](/microsoft-365-app-certification/docs/certification). 

- **Czy ta funkcja jest taka sama jak Galeria aplikacji usługi Azure AD?** Nie — weryfikacja wydawcy to komplementarny, ale oddzielny program do [galerii aplikacji Azure Active Directory](/azure/active-directory/azuread-dev/howto-app-gallery-listing). Deweloperzy, którzy spełniają powyższe kryteria, powinni zakończyć proces weryfikacji wydawcy niezależnie od udziału w tym programie. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się [, jak oznaczyć aplikację jako zweryfikowaną przez wydawcę](mark-app-as-publisher-verified.md).
* [Rozwiązywanie problemów z](troubleshoot-publisher-verification.md) weryfikacją wydawcy.