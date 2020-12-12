---
title: Co to jest współpraca B2B w Azure Active Directory?
description: Model współpracy w usłudze Azure Active Directory B2B przyznaje prawa dostępu użytkownika-gościa, aby móc bezpiecznie udostępniać zasoby i współpracować z partnerami zewnętrznymi.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 029157f5f651e150fa89ea22173c08bbf21682df
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355090"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Co to jest dostęp użytkowników-gości w usłudze Azure Active Directory B2B?

Azure Active Directory (Azure AD) współpraca między firmami (B2B, Business-to-Business) to funkcja w ramach tożsamości zewnętrznych, która umożliwia Zapraszanie użytkowników-Gości do współpracy z organizacją. Dzięki współpracy B2B możesz bezpiecznie udostępniać aplikacje i usługi firmy użytkownikom-Gościom z innych organizacji, zachowując kontrolę nad danymi firmowymi. Pracuj bezpiecznie z partnerami zewnętrznymi, dużymi lub małymi, nawet jeśli nie korzystają oni z usługi Azure AD lub nie mają działu IT. Prosty proces zapraszania i realizacji umożliwia partnerom uzyskiwanie dostępu do zasobów firmy przy użyciu własnych poświadczeń. Deweloperzy mogą używać międzyfirmowych interfejsów API usługi Azure AD do dostosowywania procesu zapraszania lub pisania aplikacji, takich jak samoobsługowe portale tworzenia kont. Informacje o licencjonowaniu i cenach związane z użytkownikami gościa znajdują się w [cenniku Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).  

> [!IMPORTANT]
> - **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).
> - **Od 31 marca 2021** firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Współpraca z dowolnym partnerem z użyciem jego tożsamości

Dzięki usłudze Azure AD B2B partner używa własnego rozwiązania do zarządzania tożsamościami, dlatego nie ma żadnych zewnętrznych kosztów administracyjnych dla organizacji. Użytkownicy-goście logują się do aplikacji i usług za pomocą własnych tożsamości służbowych lub społecznościowych.

- Partner korzysta z własnych tożsamości i poświadczeń; usługa Azure AD nie jest wymagana.
- Nie trzeba zarządzać zewnętrznymi kontami lub hasłami.
- Nie trzeba zsynchronizować konta ani zarządzać cyklami życia kont.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Łatwe Zapraszanie użytkowników-Gości z portalu usługi Azure AD

Jako administrator możesz łatwo dodawać użytkowników-gości do organizacji w witrynie Azure Portal.

- [Utwórz nowego użytkownika-gościa](b2b-quickstart-add-guest-users-portal.md) w usłudze Azure AD, podobnie jak w przypadku dodania nowego użytkownika.
- Przypisywanie użytkowników-Gości do aplikacji lub grup.
- Wyślij wiadomość e-mail z zaproszeniem zawierającą link do wykupu lub Wyślij bezpośredni link do aplikacji, którą chcesz udostępnić.

![Zrzut ekranu przedstawiający nową stronę wpisu zaproszenia użytkownika-gościa](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Aby zalogować się, użytkownicy-Goście wykonują kilka prostych [kroków](redemption-experience.md) realizacji.

![Zrzut ekranu przedstawiający stronę przegląd uprawnień](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Bezpieczne udostępnianie aplikacji i usług przy użyciu zasad

Możesz użyć zasad autoryzacji do ochrony zawartości firmowej. Zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe, można wymusić:

- Na poziomie dzierżawy.
- Na poziomie aplikacji.
- W przypadku określonych gości w celu ochrony aplikacji i danych firmowych.

![Zrzut ekranu przedstawiający opcję dostępu warunkowego](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Zezwalanie właścicielom aplikacji i grup na zarządzanie własnymi użytkownikami-gośćmi

Zarządzanie użytkownikami-gośćmi można delegować do właścicieli aplikacji, aby mogli oni bezpośrednio dodawać użytkowników-gości do dowolnej aplikacji firmy Microsoft lub innej firmy.

- Administratorzy konfigurują samoobsługowe zarządzanie aplikacjami i grupami.
- Użytkownicy inni niż administratorzy używają własnego [panelu dostępu](https://myapps.microsoft.com) w celu dodania użytkowników-gości do aplikacji lub grup.

![Zrzut ekranu przedstawiający panel dostępu dla użytkownika-gościa](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Dostosowywanie środowiska dołączania dla użytkowników gościa B2B

Przenieś partnerów zewnętrznych na tablicę w sposób dostosowany do potrzeb organizacji.

- [Zarządzanie prawami](../governance/entitlement-management-overview.md) dostępu w usłudze Azure AD umożliwia konfigurowanie zasad służących do [zarządzania dostępem użytkowników zewnętrznych](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Użyj [interfejsów API zaproszenia współpracy B2B](/graph/api/resources/invitation) , aby dostosować środowiska dołączania.

## <a name="integrate-with-identity-providers"></a>Integracja z dostawcami tożsamości

Usługa Azure AD obsługuje zewnętrznych dostawców tożsamości, takich jak Facebook, Microsoft accounts, Google i Enterprise Identity Providers. Można skonfigurować Federacji z dostawcami tożsamości, aby umożliwić użytkownikom zewnętrznym logowanie się przy użyciu istniejących kont społecznościowych lub korporacyjnych zamiast tworzenia nowego konta tylko dla aplikacji. Dowiedz się więcej o dostawcach tożsamości dla tożsamości zewnętrznych.

![Zrzut ekranu przedstawiający stronę dostawcy tożsamości](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Tworzenie przepływu użytkownika samoobsługowego tworzenia konta (wersja zapoznawcza)

Korzystając z samoobsługowego przepływu użytkowników, możesz utworzyć konto dla użytkowników zewnętrznych, którzy chcą uzyskać dostęp do aplikacji. W ramach przepływu rejestracji można udostępnić opcje dla różnych dostawców tożsamości społecznościowych lub korporacyjnych i zbierać informacje o użytkowniku. Dowiedz się więcej o rejestrowaniu samoobsługowym [i sposobach jego konfiguracji](self-service-sign-up-overview.md).

[Łączników interfejsu API](api-connectors-overview.md) można także użyć do zintegrowania przepływów użytkownika samoobsługowego rejestrowania z zewnętrznymi systemami w chmurze. Możesz łączyć się z niestandardowymi przepływami pracy zatwierdzania, przeprowadzać weryfikację tożsamości, weryfikować informacje udostępniane przez użytkownika i nie tylko.

![Zrzut ekranu przedstawiający stronę przepływów użytkownika](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Następne kroki

- [Cennik tożsamości zewnętrznych](external-identities-pricing.md)
- [Add B2B collaboration guest users in the portal (Dodawanie użytkowników-gości współpracy B2B w portalu)](add-users-administrator.md)
- [Understand the invitation redemption process (Opis procesu realizacji zaproszenia)](redemption-experience.md)