---
title: Co to jest współpraca B2B w Azure Active Directory?
description: Model współpracy w usłudze Azure Active Directory B2B przyznaje prawa dostępu użytkownika-gościa, aby móc bezpiecznie udostępniać zasoby i współpracować z partnerami zewnętrznymi.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0effb6bb6e3d3f80775a3cee1ad8a63013a78805
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585836"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Co to jest dostęp użytkowników-gości w usłudze Azure Active Directory B2B?

Model współpracy między firmami (B2B) w usłudze Azure Active Directory (Azure AD) umożliwia bezpieczne udostępnianie aplikacji i usług firmy użytkownikom-gościom z dowolnej organizacji, zachowując kontrolę nad własnymi danymi firmowymi. Pracuj bezpiecznie z partnerami zewnętrznymi, dużymi lub małymi, nawet jeśli nie korzystają oni z usługi Azure AD lub nie mają działu IT. Prosty proces zapraszania i realizacji umożliwia partnerom uzyskiwanie dostępu do zasobów firmy przy użyciu własnych poświadczeń. Deweloperzy mogą używać międzyfirmowych interfejsów API usługi Azure AD do dostosowywania procesu zapraszania lub pisania aplikacji, takich jak samoobsługowe portale tworzenia kont.

Obejrzyj film wideo, aby dowiedzieć się, jak bezpiecznie współpracować z użytkownikami-gośćmi, zapraszając ich do logowania do aplikacji i usług firmy przy użyciu ich własnych tożsamości.

Poniższy film oferuje przydatne omówienie tej możliwości.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > **Od 31 marca 2021**firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Współpraca z dowolnym partnerem z użyciem jego tożsamości

Dzięki usłudze Azure AD B2B partner używa własnego rozwiązania do zarządzania tożsamościami, dlatego nie ma żadnych zewnętrznych kosztów administracyjnych dla organizacji.

- Partner korzysta z własnych tożsamości i poświadczeń; usługa Azure AD nie jest wymagana.
- Nie trzeba zarządzać zewnętrznymi kontami lub hasłami.
- Nie trzeba zsynchronizować konta ani zarządzać cyklami życia kont.  

![Zrzut ekranu przedstawiający stronę Dodawanie elementów członkowskich](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Zapraszanie użytkowników-gości za pomocą prostego procesu zapraszania i realizacji

Użytkownicy-goście logują się do aplikacji i usług za pomocą własnych tożsamości służbowych lub społecznościowych. Jeśli użytkownik-gość nie ma konta Microsoft lub konta usługi Azure AD, zostanie ono utworzone, gdy użytkownik zrealizuje zaproszenie. 

- Zaproś użytkowników-gości przy użyciu wybranych przez nich tożsamości poczty e-mail.
- Wyślij bezpośredni link do aplikacji lub wyślij zaproszenie do własnego portalu dostępu użytkownika-gościa.
- Użytkownicy-goście wykonują kilka prostych kroków w celu realizacji.

![Zrzut ekranu przedstawiający stronę przegląd uprawnień](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Bezpieczne udostępnianie aplikacji i usług przy użyciu zasad

Możesz użyć zasad autoryzacji do ochrony zawartości firmowej. Zasady dostępu warunkowego, takie jak uwierzytelnianie wieloskładnikowe, można wymusić:

- Na poziomie dzierżawy.
- Na poziomie aplikacji.
- W przypadku określonych gości w celu ochrony aplikacji i danych firmowych.

![Zrzut ekranu przedstawiający opcję dostępu warunkowego](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Łatwe dodawanie użytkowników-gości w portalu usługi Azure AD

Jako administrator możesz łatwo dodawać użytkowników-gości do organizacji w witrynie Azure Portal.

- Utwórz nowego użytkownika-gościa w usłudze Azure AD podobnie jak w przypadku dodawania nowego użytkownika.
- Użytkownik-gość natychmiast otrzymuje możliwe do dostosowania zaproszenie, które pozwala mu na zalogowanie się do panelu dostępu.
- Użytkowników-gości w katalogu można przypisywać do aplikacji lub grup.  

![Zrzut ekranu przedstawiający nową stronę wpisu zaproszenia użytkownika-gościa](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Zezwalanie właścicielom aplikacji i grup na zarządzanie własnymi użytkownikami-gośćmi

Zarządzanie użytkownikami-gośćmi można delegować do właścicieli aplikacji, aby mogli oni bezpośrednio dodawać użytkowników-gości do dowolnej aplikacji firmy Microsoft lub innej firmy.

- Administratorzy konfigurują samoobsługowe zarządzanie aplikacjami i grupami.
- Użytkownicy inni niż administratorzy używają własnego [panelu dostępu](https://myapps.microsoft.com) w celu dodania użytkowników-gości do aplikacji lub grup.

![Zrzut ekranu przedstawiający panel dostępu dla użytkownika-gościa](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Dostosowywanie środowiska dołączania dla użytkowników gościa B2B

Zaproś partnerów zewnętrznych do współpracy w sposób dostosowany do potrzeb swojej organizacji.

- [Zarządzanie prawami](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) dostępu w usłudze Azure AD umożliwia konfigurowanie zasad służących do [zarządzania dostępem użytkowników zewnętrznych](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).
- Użyj [interfejsów API zaproszenia współpracy B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) , aby dostosować środowiska dołączania.

## <a name="integrate-with-identity-providers"></a>Integracja z dostawcami tożsamości

Usługa Azure AD obsługuje zewnętrznych dostawców tożsamości, takich jak Facebook, Microsoft accounts, Google i Enterprise Identity Providers. Można skonfigurować Federacji z dostawcami tożsamości, aby umożliwić użytkownikom zewnętrznym logowanie się przy użyciu istniejących kont społecznościowych lub korporacyjnych zamiast tworzenia nowego konta tylko dla aplikacji. Dowiedz się więcej o dostawcach tożsamości dla tożsamości zewnętrznych.

![Zrzut ekranu przedstawiający stronę dostawcy tożsamości](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Tworzenie przepływu użytkownika samoobsługowego tworzenia konta (wersja zapoznawcza)

Korzystając z samoobsługowego przepływu użytkowników, możesz utworzyć konto dla użytkowników zewnętrznych, którzy chcą uzyskać dostęp do aplikacji. W ramach przepływu rejestracji można udostępnić opcje dla różnych dostawców tożsamości społecznościowych lub korporacyjnych i zbierać informacje o użytkowniku. Dowiedz się więcej o rejestrowaniu samoobsługowym i sposobach jego konfiguracji.

![Zrzut ekranu przedstawiający stronę przepływów użytkownika](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)

## <a name="next-steps"></a>Następne kroki

- [Licensing guidance for Azure AD B2B collaboration (Wskazówki dotyczące licencjonowania w ramach współpracy B2B w usłudze Azure AD)](licensing-guidance.md)
- [Add B2B collaboration guest users in the portal (Dodawanie użytkowników-gości współpracy B2B w portalu)](add-users-administrator.md)
- [Understand the invitation redemption process (Opis procesu realizacji zaproszenia)](redemption-experience.md)
- Jak zawsze możesz skontaktować się z zespołem ds. produktu, aby podzielić się opiniami, problemami i sugestiami za pośrednictwem naszej [społeczności technicznej firmy Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
