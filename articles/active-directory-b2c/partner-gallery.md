---
title: Galeria partnerów ISV dla Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować usługę z naszymi partnerami isV, aby dostosować środowisko użytkownika końcowego do Twoich potrzeb. Nasza sieć partnerska rozszerza możliwości naszego rozwiązania; włączanie uwierzytelniania wieloskładnikowego, bezpiecznego uwierzytelniania klienta, kontroli dostępu opartej na rolach; zniechęć do oszustwa za pomocą weryfikacji tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b555c8651c6e1608193a6ae06c39b20f633e4ea9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813667"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C isv partners

Nasza sieć partnerów isV rozszerza możliwości naszego rozwiązania, aby ułatwić tworzenie bezproblemowych doświadczeń użytkowników końcowych. Za pomocą usługi Azure AD B2C można zintegrować usługę z partnerami ISV, aby włączyć metody uwierzytelniania wieloskładnikowego (MFA), wykonać kontrolę dostępu opartą na rolach, włączyć weryfikację i weryfikację tożsamości, zwiększyć bezpieczeństwo dzięki wykrywaniu botów i ochronie przed oszustwami oraz spełnić wymagania dyrektywy 2 (PSD2) bezpiecznego uwierzytelniania klientów (SCA, Payment Services Directive 2). Skorzystaj z naszych szczegółowych przykładowych przewodników, aby dowiedzieć się, jak zintegrować aplikacje z partnerami ISV.

>[!NOTE]
>Witryna [Azure Active Directory B2C w witrynie GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) zawiera również przykładowe zasady niestandardowe od społeczności.

## <a name="identity-verification-and-proofing"></a>Weryfikacja i weryfikacja tożsamości

Firma Microsoft współpracuje z następującymi isvami w celu weryfikacji i weryfikacji tożsamości.

| Partner ISV | Opis i wskazówki dotyczące integracji |
|:-------------------------|:--------------|
|![Zrzut ekranu przedstawiający logo experian.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) to dostawca weryfikacji i weryfikacji tożsamości, który przeprowadza oceny ryzyka na podstawie atrybutów użytkownika, aby zapobiec oszustwom. |
|![Zrzut ekranu przedstawiający logo IDology.](./media/partner-gallery/idology-logo.png) | [IDology to](./partner-idology.md) dostawca weryfikacji i weryfikacji tożsamości z rozwiązaniami do weryfikacji identyfikatorów, rozwiązaniami do zapobiegania oszustwom, rozwiązaniami do sprawdzania zgodności i innymi.|
|![Zrzut ekranu przedstawiający logo Jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio to](./partner-jumio.md) usługa weryfikacji identyfikatorów, która umożliwia automatyczną weryfikację identyfikatorów w czasie rzeczywistym, chroniąc dane klientów. |
| ![Zrzut ekranu przedstawiający logo LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) to dostawca profilowania i weryfikacji tożsamości, który weryfikuje identyfikację użytkownika i zapewnia kompleksową ocenę ryzyka na podstawie urządzenia użytkownika. |
| ![Zrzut ekranu przedstawiający logo Onfido](./media/partner-gallery/onfido-logo.png) | [Onfido to](./partner-onfido.md) rozwiązanie do weryfikacji identyfikatora dokumentu i  biometrii twarzy, które umożliwia firmom spełnienie wymagań w zakresie rozpoznawania klientów i tożsamości w czasie rzeczywistym.  |

## <a name="mfa-and-passwordless-authentication"></a>Uwierzytelnianie wieloskładnikowe i uwierzytelnianie bez hasła

Firma Microsoft współpracuje z następującymi isvami w celu uwierzytelniania wieloskładnikowego i bez hasła.

| Partner isv | Opis i wskazówki dotyczące integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo hypr](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) to dostawca uwierzytelniania bez hasła, który zastępuje hasła szyfrowaniem kluczy publicznych, eliminując oszustwa, wyłudzanie informacji i ponowne używanie poświadczeń. |
| ![Zrzut ekranu przedstawiający logo itsme](./media/partner-gallery/itsme-logo.png) | [Itsme](./partner-itsme.md) to rozwiązanie do identyfikacji elektronicznej, uwierzytelniania i zaufania usług cyfrowych (eiDAS), które umożliwia użytkownikom bezpieczne logowanie się bez czytników kart, haseł, uwierzytelniania dwuskładnikowego i wielu kodów PIN. |
|![Zrzut ekranu przedstawiający logo bez kluczy.](./media/partner-gallery/keyless-logo.png) | [Bez klucza to](./partner-keyless.md) dostawca uwierzytelniania bez hasła, który zapewnia uwierzytelnianie w formie skanowania biometrycznego twarzy i eliminuje oszustwa, wyłudzanie informacji i ponowne użycie poświadczeń.
| ![Zrzut ekranu przedstawiający logo nevis](./media/partner-gallery/nevis-logo.png) | [Aplikacja Nevis](./partner-nevis.md) umożliwia uwierzytelnianie bez hasła i zapewnia w pełni markowe środowisko użytkownika końcowego oparte na urządzeniach przenośnych z aplikacją Nevis Access na potrzeby silnego uwierzytelniania klientów i zapewnienia zgodności z wymaganiami dotyczącymi transakcji PSD2. |
| ![Zrzut ekranu przedstawiający logo trusona](./media/partner-gallery/trusona-logo.png) | [Integracja rozwiązania Trusona](./partner-trusona.md) ułatwia bezpieczne logowanie i umożliwia uwierzytelnianie bez hasła, uwierzytelnianie wieloskładnikowe i skanowanie licencji cyfrowych. |
| ![Zrzut ekranu przedstawiający logo usługi twilio.](./media/partner-gallery/twilio-logo.png) | [Aplikacja Weryfikowanie usługi Twilio](./partner-twilio.md) oferuje wiele rozwiązań umożliwiających uwierzytelnianie wieloskładnikowe za pośrednictwem haseł jednorazowych (OTP, time-based one-time password) i powiadomień wypychanych oraz zapewnianie zgodności z wymaganiami usługi SCA dla psd2. |
| ![Zrzut ekranu z wpisaną logoDNA](./media/partner-gallery/typingdna-logo.png) | [Funkcja TypingDNA](./partner-typingdna.md) umożliwia silne uwierzytelnianie klientów, analizując wzorzec wpisywania użytkownika. Pomaga to firmom włączyć dyskretną usługę MFA i spełnić wymagania dotyczące usługi SCA dla psd2. |
| ![Zrzut ekranu przedstawiający logo whoiam](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) to aplikacja BRANDED Identity Management System (BRIMS), która umożliwia organizacjom weryfikowanie bazy użytkowników za pomocą głosu, wiadomości SMS i wiadomości e-mail. |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach 
 
Firma Microsoft współpracuje z następującymi tabelami ISV w celu kontroli dostępu opartej na rolach.

| Partner ISV | Opis i wskazówki dotyczące integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo n8identity](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) to platforma do zarządzania tożsamością jako usługą, która zapewnia rozwiązanie dotyczące migracji kont klientów i administrowania żądaniami obsługi klienta (CSR) uruchomionymi na Microsoft Azure. |
| ![Zrzut ekranu przedstawiający logo Saviynt](./media/partner-gallery/saviynt-logo.png) | [Inteligentna platforma natywna](./partner-Saviynt.md) dla chmury wspiera lepsze zabezpieczenia, zgodność i ład dzięki inteligentnej analizie i integracji między aplikacjami w celu usprawniania modernizacji IT. |

## <a name="secure-hybrid-access-to-on-premises-application"></a>Bezpieczny dostęp hybrydowy do aplikacji lokalnej

Firma Microsoft współpracuje z następującymi isvami w celu zapewnienia bezpiecznego dostępu hybrydowego do aplikacji lokalnej. 

| Partner ISV | Opis i wskazówki dotyczące integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo ping](./media/partner-gallery/ping-logo.png) | [Tożsamość ping](./partner-ping-identity.md) umożliwia bezpieczny dostęp hybrydowy do starszych aplikacji lokalnych w wielu chmurach. |
| ![Zrzut ekranu przedstawiający logo warstwy](./media/partner-gallery/strata-logo.png) | [Warstwa Strata](./partner-strata.md) zapewnia bezpieczny dostęp hybrydowy do aplikacji lokalnych, wymuszając spójne zasady dostępu, zachowując synchronizację tożsamości i ułatwiając przechodzenie aplikacji ze starszych systemów tożsamości do uwierzytelniania opartego na standardach i kontroli dostępu zapewnianych przez Azure AD B2C. |
| ![Zrzut ekranu przedstawiający logo zscaler](./media/partner-gallery/zscaler-logo.png) | [Rozwiązanie Zscaler](./partner-zscaler.md) zapewnia oparty na zasadach, bezpieczny dostęp do prywatnych aplikacji i zasobów bez poniesień kosztów, problemów i zagrożeń dla bezpieczeństwa sieci VPN. |

## <a name="fraud-protection"></a>Ochrona przed oszustwem

Firma Microsoft współpracuje z następującymi isvami w celu wykrywania oszustw i zapobiegania im. 

| Partner isv | Opis i wskazówki dotyczące integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo laboratorium Firmy Arkose](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) to dostawca rozwiązań do zapobiegania oszustwom, który pomaga organizacjom chronić przed atakami botów, atakami na konta i fałszywym otwieraniem kont. |
| ![Zrzut ekranu przedstawiający logo biocatch](./media/partner-gallery/biocatch-logo.png) | [BioCatch](./partner-biocatch.md) to dostawca rozwiązań do zapobiegania oszustwom, który analizuje fizyczne i poznawcze zachowania cyfrowe użytkownika w celu generowania szczegółowych informacji odróżniających uprawnionych klientów od cyberprzestępczych. |
| ![Zrzut ekranu przedstawiający logo usługi Microsoft Dynamics 365](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 Fraud Protection](./partner-dynamics-365-fraud-protection.md) to rozwiązanie, które pomaga organizacjom chronić przed fałszywym otwieraniem kont za pomocą odcisku palca urządzenia. |


## <a name="additional-information"></a>Dodatkowe informacje

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w programie Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="next-steps"></a>Następne kroki

W wymienionych tabelach wybierz partnera, aby dowiedzieć się, jak zintegrować swoje rozwiązanie z Azure AD B2C.
