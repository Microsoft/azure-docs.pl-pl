---
title: Uwierzytelnianie a autoryzacja | Azure
titleSuffix: Microsoft identity platform
description: Poznaj podstawowe informacje na temat uwierzytelniania i autoryzacji w usłudze Microsoft Identity platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d4abc9e1bba151f46adf71dd0185ddddad916a38
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216465"
---
# <a name="authentication-vs-authorization"></a>Uwierzytelnianie i autoryzacja

Ten artykuł definiuje uwierzytelnianie i autoryzację. Zawarto również krótko, jak używać platformy tożsamości firmy Microsoft do uwierzytelniania i autoryzowania użytkowników w aplikacjach sieci Web, interfejsach API sieci Web lub aplikacjach wywołujących chronione interfejsy API sieci Web. Jeśli zobaczysz termin, którego nie znasz, wypróbuj nasz [słownik](developer-glossary.md) lub nasze [wideo platformy tożsamości firmy Microsoft](identity-videos.md), które obejmują podstawowe pojęcia.

## <a name="authentication"></a>Authentication

*Uwierzytelnianie* to proces potwierdzania, że jesteś osobą. Czasami jest ona skracana do *AuthN*. Platforma tożsamości firmy Microsoft używa protokołu [Connect OpenID Connect](https://openid.net/connect/) do obsługi uwierzytelniania.

## <a name="authorization"></a>Autoryzacja

*Autoryzacja* to czynność udzielenia uprawnienia uwierzytelnionej stronie. Określa dane, do których można uzyskać dostęp i co można zrobić z tymi danymi. Autoryzacja jest czasami skracana do *autoryzacji*. Platforma tożsamości firmy Microsoft używa protokołu [OAuth 2,0](https://oauth.net/2/) do obsługi autoryzacji.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft

Tworzenie aplikacji, które zachowują własne informacje o nazwie użytkownika i hasła, wiąże się z dużym obciążeniem administracyjnym, gdy trzeba dodać lub usunąć użytkowników w wielu aplikacjach. Zamiast tego aplikacje mogą delegować tę odpowiedzialność do scentralizowanego dostawcy tożsamości.

Azure Active Directory (Azure AD) to scentralizowany dostawca tożsamości w chmurze. Delegowanie uwierzytelniania i autoryzacji umożliwia takie scenariusze, jak:

- Zasady dostępu warunkowego, które wymagają, aby użytkownik znajdował się w określonej lokalizacji.
- Korzystanie z [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md), które jest czasami nazywane uwierzytelnianiem dwuskładnikowym lub funkcji 2FA.
- Umożliwienie użytkownikowi logowania jednokrotnego, a następnie automatyczne zalogowanie do wszystkich aplikacji sieci Web, które współużytkują ten sam katalog scentralizowany. Ta funkcja jest nazywana logowaniem jednokrotnym *(SSO)*.

Platforma tożsamości firmy Microsoft upraszcza autoryzację i uwierzytelnianie dla deweloperów aplikacji, zapewniając tożsamość jako usługę. Obsługuje ona standardowe protokoły i biblioteki Open Source dla różnych platform, które ułatwiają szybkie rozpoczęcie tworzenia kodu. Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują się do wszystkich tożsamości firmy Microsoft, uzyskują tokeny do wywoływania [Microsoft Graph](https://developer.microsoft.com/graph/), uzyskiwania dostępu do interfejsów API firmy Microsoft lub uzyskiwania dostępu do innych interfejsów API utworzonych przez deweloperów.

Ten film wideo wyjaśnia platformę tożsamości firmy Microsoft i podstawowe informacje dotyczące nowoczesnego uwierzytelniania: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Poniżej przedstawiono porównanie protokołów używanych przez platformę tożsamości firmy Microsoft:

* **Połączenie OAuth i OpenID Connect**: platforma używa protokołu OAuth do autoryzacji i OpenID Connect Connect (OIDC) do uwierzytelniania. OpenID Connect Connect jest tworzona na podstawie protokołu OAuth 2,0, więc Terminologia i przepływ są podobne między nimi. Można nawet uwierzytelnić użytkownika (za pomocą usługi OpenID Connect Connect) i uzyskać autoryzację w celu uzyskania dostępu do chronionego zasobu, którego właścicielem jest użytkownik (za pomocą protokołu OAuth 2,0). Aby uzyskać więcej informacji, zobacz [Protokoły OAuth 2,0 i OpenID Connect Connect](active-directory-v2-protocols.md) [Protocols i OpenID Connect Connect](v2-protocols-oidc.md)
* **OAuth a SAML**: platforma używa protokołu OAuth 2,0 do autoryzacji i protokołu SAML do uwierzytelniania. Aby uzyskać więcej informacji na temat sposobu korzystania z tych protokołów razem zarówno do uwierzytelniania użytkownika, jak i uzyskiwania autoryzacji dostępu do chronionego zasobu, zobacz [Microsoft Identity platform i OAuth 2,0 SAML Flow Assertion](v2-saml-bearer-assertion.md).
* **OpenID Connect nawiązuje połączenie z SAML**: platforma używa zarówno połączenia OpenID Connect, jak i SAML do uwierzytelniania użytkownika i włączania logowania jednokrotnego. Uwierzytelnianie SAML jest często używane z dostawcami tożsamości, takimi jak Active Directory Federation Services (AD FS) federacyjnego do usługi Azure AD, więc jest często używana w aplikacjach dla przedsiębiorstw. OpenID Connect Connect jest często używana w przypadku aplikacji, które są wyłącznie w chmurze, takich jak aplikacje mobilne, witryny internetowe i interfejsy API sieci Web.

## <a name="next-steps"></a>Następne kroki

W przypadku innych tematów obejmujących podstawowe informacje dotyczące uwierzytelniania i autoryzacji:

* Aby dowiedzieć się, jak tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów są używane w autoryzacji i uwierzytelnianiu, zobacz [tokeny zabezpieczające](security-tokens.md).
* Aby dowiedzieć się więcej o procesie rejestracji aplikacji, aby umożliwić integrację z platformą tożsamości firmy Microsoft, zobacz [model aplikacji](application-model.md).
