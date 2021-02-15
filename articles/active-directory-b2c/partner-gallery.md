---
title: Galeria partnerów niezależnych dostawców oprogramowania dla Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować się z partnerami niezależnego dostawcy oprogramowania, aby dostosować środowisko użytkownika końcowego do Twoich potrzeb. Nasza sieć partnerskia rozszerza możliwości rozwiązania. Włączenie usługi MFA, bezpieczne uwierzytelnianie klienta, kontrola dostępu oparta na rolach; zwalczanie oszustw przy użyciu weryfikacji tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1ee632e3aaae7b858ab43b45f6e72aff8d1fb77
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361772"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C partnerów niezależnych dostawców oprogramowania

Nasza sieć partnera niezależnego dostawcy oprogramowania rozszerza możliwości rozwiązań, które ułatwiają tworzenie bezproblemowe środowiska użytkownika końcowego. Za pomocą Azure AD B2C można zintegrować z partnerami niezależnego dostawcy oprogramowania, aby włączyć metody uwierzytelniania wieloskładnikowego (MFA), do kontroli dostępu opartej na rolach, włączyć weryfikację tożsamości i sprawdzanie poprawności, poprawić zabezpieczenia dzięki bot wykrywania i ochrony przed oszustwami oraz spełnić wymagania dotyczące zgodności z przepisami dyrektywy 2 (PSD2) bezpieczne uwierzytelnianie klienta (SCA). Skorzystaj z naszych szczegółowych przykładów, aby dowiedzieć się, jak zintegrować aplikacje z partnerami niezależnego dostawcy oprogramowania.

>[!NOTE]
>[Witryna społeczności Azure Active Directory B2C w serwisie GitHub](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) udostępnia także przykładowe zasady niestandardowe społeczności.

## <a name="identity-verification-and-proofing"></a>Weryfikacja tożsamości i sprawdzanie poprawności

Dzięki Azure AD B2C partnerom klienci mogą włączyć weryfikację tożsamości i sprawdzanie ich użytkowników końcowych przed zezwoleniem na rejestrację lub dostęp do konta. Weryfikacja tożsamości i sprawdzanie poprawności mogą sprawdzić dokument, informacje oparte na wiedzy oraz ich stan.

Diagram architektury wysokiego poziomu objaśnia przepływ.

![Diagram przedstawia przepływ weryfikacji tożsamości](./media/partner-gallery/third-party-identity-proofing.png)

Partnerzy firmy Microsoft z następującymi dostawcami oprogramowania (ISV) do weryfikacji tożsamości i sprawdzania poprawności.

| Partner niezależnego dostawcy oprogramowania | Wskazówki dotyczące opisu i integracji |
|:-------------------------|:--------------|
|![Zrzut ekranu przedstawiający logo Experian.](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) jest weryfikacją tożsamości i dostawcą sprawdzającym, który przeprowadza ocenę ryzyka na podstawie atrybutów użytkownika w celu zapobiegania oszustwom. |
|![Zrzut ekranu przedstawiający logo IDology.](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) to weryfikacja tożsamości i dostawca weryfikacji z rozwiązaniami weryfikacji identyfikatora, rozwiązaniami zapobiegania oszustwom, rozwiązaniami zgodności i innymi.|
|![Zrzut ekranu przedstawiający logo Jumio.](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) to usługa weryfikacji identyfikatora, która umożliwia automatyczne weryfikację identyfikatora w czasie rzeczywistym, chroniąc dane klientów. |
| ![Zrzut ekranu przedstawiający logo LexisNexis.](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) jest dostawcą profilowania i weryfikacji tożsamości, który weryfikuje tożsamość użytkownika i zapewnia kompleksową ocenę ryzyka opartą na urządzeniu użytkownika. |
| ![Zrzut ekranu przedstawiający logo Onfido](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) to identyfikator dokumentu oraz rozwiązanie do weryfikacji twarzy biometryczne, które umożliwia firmom zaspokajanie wymagań *klienta* i tożsamości w czasie rzeczywistym.  |

## <a name="mfa-and-passwordless-authentication"></a>Uwierzytelnianie MFA i bezhasło

Partnerzy firmy Microsoft z następującymi dostawcami oprogramowania dla uwierzytelniania MFA i bezhaseł.

| Partner niezależnego dostawcy oprogramowania | Wskazówki dotyczące opisu i integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo hypr](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) to dostawca uwierzytelniania bez hasła, który zastępuje hasła z szyfrowaniem klucza publicznego eliminujące oszustwo, phishing i ponowne użycie poświadczeń. |
| ![Zrzut ekranu przedstawiający logo itsme](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) to elektroniczne rozwiązanie do identyfikacji, uwierzytelniania i usług zaufania (eiDAS), które umożliwia użytkownikom bezpieczne logowanie bez czytników kart, haseł, uwierzytelniania dwuskładnikowego i wielu kodów PIN. |
|![Zrzut ekranu przedstawiający logo bez znaku.](./media/partner-gallery/keyless-logo.png) | Funkcja [less](./partner-keyless.md) nie jest dostawcą uwierzytelniania bez hasła, który zapewnia uwierzytelnianie w formie skanowania biometrycznego typu twarzy i eliminuje oszustwo, phishing i ponowne użycie poświadczeń.
| ![Zrzut ekranu przedstawiający logo firmy Nevis](./media/partner-gallery/nevis-logo.png) | Program [Nevis](./partner-nevis.md) umożliwia uwierzytelnianie bez haseł i zapewnia mobilny, w pełni oznakowany interfejs użytkownika końcowego w celu uzyskania silnego uwierzytelniania klienta i zapewnienia zgodności z wymaganiami transakcji PSD2. |
| ![Zrzut ekranu przedstawiający logo trusona](./media/partner-gallery/trusona-logo.png) | Integracja usługi [Trusona](./partner-trusona.md) pomaga w bezpiecznym logowaniu i umożliwia korzystanie z uwierzytelniania bez hasła, usługi MFA i skanowania licencji cyfrowych. |
| ![Zrzut ekranu przedstawiający logo Twilio.](./media/partner-gallery/twilio-logo.png) | [Twilio Sprawdź, czy aplikacja](./partner-twilio.md) udostępnia wiele rozwiązań umożliwiających korzystanie z usługi MFA za pośrednictwem hasła jednorazowego (OTP) usługi SMS, hasła jednorazowego (TOTP) i powiadomień wypychanych oraz zgodność z wymaganiami SCA dla PSD2. |
| ![Zrzut ekranu przedstawiający logo typingDNA](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) umożliwia silne uwierzytelnianie klienta przez analizowanie wzorca wpisywania przez użytkownika. Pomaga firmom w włączaniu dyskretnej usługi MFA i spełnianiu wymagań SCA dla PSD2. |
| ![Zrzut ekranu przedstawiający logo whoiam](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) to aplikacja typu "Mark Management System" (BRIMS), która umożliwia organizacjom Weryfikowanie bazy użytkowników przy użyciu głosu, wiadomości SMS i wiadomości e-mail. |

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach 
 
Partnerzy firmy Microsoft z następującymi niezależnymi dostawcami oprogramowania do kontroli dostępu opartej na rolach.

| Partner niezależnego dostawcy oprogramowania | Wskazówki dotyczące opisu i integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo n8identity](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) to platforma ładu typu "tożsamość jako usługa", która zapewnia rozwiązanie do obsługi migracji kont klientów i administrowania żądaniami obsługi klienta (CSR) działającym na Microsoft Azure. |
| ![Zrzut ekranu przedstawiający logo Saviynt](./media/partner-gallery/saviynt-logo.png) | Platforma [Saviynt](./partner-Saviynt.md) w chmurze zapewnia lepsze zabezpieczenia, zgodność i nadzór dzięki inteligentnej analizie i integracji między aplikacjami w celu usprawnienia modernizacji. |

## <a name="security"></a>Zabezpieczenia

W celu zapewnienia bezpieczeństwa partnerzy firmy Microsoft z następującymi dostawcami oprogramowania.

| Partner niezależnego dostawcy oprogramowania | Wskazówki dotyczące opisu i integracji |
|:-------------------------|:--------------|
| ![Zrzut ekranu przedstawiający logo laboratorium Arkose](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) to dostawca rozwiązań zapobiegania oszustwom, który pomaga organizacjom chronić przed atakami bot, atakami polegającymi na przejęciu kont i nieuczciwych otwartych kontach. |
| ![Zrzut ekranu przedstawiający logo systemu Microsoft Dynamics 365](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Ochrona oszustw w systemie Microsoft Dynamics 365](./partner-dynamics-365-fraud-protection.md) to rozwiązanie, które ułatwia organizacjom ochronę przed nieuczciwymi procesami otwierania konta za pomocą odcisku palca urządzeń. |
| ![Zrzut ekranu przedstawiający logo polecenia ping](./media/partner-gallery/ping-logo.png) | [Polecenie ping Identity](./partner-ping-identity.md) umożliwia bezpieczny dostęp hybrydowy do lokalnych starszych aplikacji w wielu chmurach. |
| ![Zrzut ekranu przedstawiający logo warstwy strat](./media/partner-gallery/strata-logo.png) | Firma [strat](./partner-strata.md) zapewnia bezpieczny dostęp hybrydowy do aplikacji lokalnych przez wymuszanie spójnych zasad dostępu, utrzymywanie tożsamości w synchronizacji oraz proste przenoszenie aplikacji ze starszych systemów tożsamości do opartych na standardach uwierzytelniania i kontroli dostępu zapewnianych przez Azure AD B2C. |
| ![Zrzut ekranu przedstawiający logo rozwiązania Zscaler](./media/partner-gallery/zscaler-logo.png) | [Rozwiązania Zscaler](./partner-zscaler.md) zapewnia oparte na zasadach, bezpieczny dostęp do prywatnych aplikacji i zasobów bez konieczności ponoszenia kosztów, problemów lub zagrożeń bezpieczeństwa sieci VPN. |

## <a name="additional-information"></a>Dodatkowe informacje

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>Następne kroki

Wybierz partnera w powyższych tabelach, aby dowiedzieć się, jak zintegrować swoje rozwiązanie z Azure AD B2C.
