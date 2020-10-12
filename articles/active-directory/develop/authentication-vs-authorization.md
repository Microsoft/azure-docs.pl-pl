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
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86252117"
---
# <a name="authentication-vs-authorization"></a>Uwierzytelnianie a autoryzacja

Ten artykuł definiuje uwierzytelnianie i autoryzację oraz krótko opisuje, jak można używać platformy tożsamości firmy Microsoft do uwierzytelniania i autoryzowania użytkowników w aplikacjach sieci Web, interfejsach API sieci Web lub aplikacjach wywołujących chronione interfejsy API sieci Web. Jeśli zobaczysz termin, którego nie znasz, wypróbuj nasz [słownik](developer-glossary.md) lub nasze [wideo platformy Microsoft Identity platform](identity-videos.md) , które obejmują podstawowe pojęcia.

## <a name="authentication"></a>Uwierzytelnianie

**Uwierzytelnianie** to proces potwierdzania użytkownika. Uwierzytelnianie jest czasami określane terminem AuthN. Platforma tożsamości firmy Microsoft implementuje protokół [Connect OpenID Connect](https://openid.net/connect/) do obsługi uwierzytelniania.

## <a name="authorization"></a>Autoryzacja

**Autoryzacja** to czynność udzielenia uprawnienia uwierzytelnionej stronie. Określa dane, do których można uzyskać dostęp i co można zrobić z tymi danymi. Autoryzacja jest czasami określana terminem AuthZ. Platforma tożsamości firmy Microsoft implementuje protokół [OAuth 2,0](https://oauth.net/2/) do obsługi autoryzacji.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft

Zamiast tworzyć aplikacje, które zachowują własne informacje o nazwie użytkownika i hasła, co wiąże się z dużym obciążeniem administracyjnym, gdy trzeba dodać lub usunąć użytkowników w wielu aplikacjach, aplikacje mogą delegować tę odpowiedzialność do scentralizowanego dostawcy tożsamości.

Azure Active Directory (Azure AD) to scentralizowany dostawca tożsamości w chmurze. Delegowanie uwierzytelniania i autoryzacji pozwala na takie scenariusze, jak zasady dostępu warunkowego, które wymagają, aby użytkownik znajdował się w określonej lokalizacji, korzystać z [uwierzytelniania wieloskładnikowego](../authentication/concept-mfa-howitworks.md) (czasami nazywanego uwierzytelnianiem dwuskładnikowym lub funkcji 2FA), a także umożliwiać użytkownikowi logowanie się jednokrotnie do wszystkich aplikacji sieci Web, które współużytkują ten sam katalog scentralizowany. Ta funkcja jest określana jako logowanie jednokrotne **(SSO)**.

Platforma tożsamości firmy Microsoft upraszcza autoryzację i uwierzytelnianie dla deweloperów aplikacji, dostarczając tożsamość jako usługę, z obsługą standardowych protokołów, takich jak OAuth 2,0 i OpenID Connect Connect, a także bibliotek typu "open source" dla różnych platform, które ułatwiają szybkie rozpoczęcie tworzenia kodu. Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują się do wszystkich tożsamości firmy Microsoft, uzyskują tokeny, aby wywoływać [Microsoft Graph](https://developer.microsoft.com/graph/), inne interfejsy API firmy Microsoft lub interfejsy API zaprojektowane przez deweloperów.

Poniżej przedstawiono krótkie porównanie różnych protokołów używanych przez platformę tożsamości firmy Microsoft:

* Uwierzytelnianie **OAuth vs OpenID Connect Connect**: protokół OAuth jest używany na potrzeby autoryzacji i OpenID Connect Connect (OIDC) do uwierzytelniania. OpenID Connect Connect jest tworzona na podstawie protokołu OAuth 2,0, więc Terminologia i przepływ są podobne między nimi. Można nawet uwierzytelnić użytkownika (za pomocą usługi OpenID Connect Connect) i uzyskać autoryzację w celu uzyskania dostępu do chronionego zasobu, którego właścicielem jest użytkownik (przy użyciu protokołu OAuth 2,0). Aby uzyskać więcej informacji, zobacz [Protokoły OAuth 2,0 i OpenID Connect Connect](active-directory-v2-protocols.md) [Protocols i OpenID Connect Connect](v2-protocols-oidc.md)
* Uwierzytelnianie **OAuth vs SAML**: protokół OAuth jest używany do autoryzacji i służy do uwierzytelniania protokołu SAML. Aby uzyskać więcej informacji o tym, jak te dwa protokoły mogą być używane razem z uwierzytelnianiem użytkownika (przy użyciu protokołu SAML) i uzyskać autoryzację w celu uzyskania dostępu do chronionego zasobu (przy użyciu uwierzytelniania OAuth 2,0), zobacz [Microsoft Identity platform i OAuth 2,0 SAML Flow](v2-saml-bearer-assertion.md) .
* **OpenID Connect Connect vs SAML**: zarówno połączenie OpenID Connect, jak i SAML są używane do uwierzytelniania użytkownika i służą do włączania logowania jednokrotnego. Uwierzytelnianie SAML jest często używane z dostawcami tożsamości, takimi jak Active Directory Federation Services (ADFS) federacyjne do usługi Azure AD i dlatego często są używane w aplikacjach dla przedsiębiorstw. OpenID Connect Connect jest często używana w przypadku aplikacji, które są wyłącznie w chmurze, takich jak aplikacje mobilne, witryny sieci Web i interfejsy API sieci Web.

## <a name="next-steps"></a>Następne kroki

W przypadku innych tematów obejmujących podstawowe informacje dotyczące uwierzytelniania i autoryzacji:

* Zobacz [tokeny zabezpieczające](security-tokens.md) , aby dowiedzieć się, jak tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów są używane w autoryzacji i uwierzytelnianiu.
* Zobacz [model aplikacji](application-model.md) , aby dowiedzieć się więcej o procesie rejestracji aplikacji, aby umożliwić integrację z platformą tożsamości firmy Microsoft.
* Aby dowiedzieć się więcej o przepływie logowania aplikacji sieci Web, komputerów stacjonarnych i aplikacji mobilnych na platformie tożsamości firmy Microsoft, zobacz temat rejestrowanie w usłudze [Flow](app-sign-in-flow.md) .

* Aby dowiedzieć się więcej na temat protokołów wdrożonych przez platformę Microsoft Identity, zobacz [Protokoły protokołu OAuth 2,0 i OpenID Connect Connect na platformie tożsamości firmy Microsoft](active-directory-v2-protocols.md).
* Aby uzyskać więcej informacji na temat obsługi rejestracji jednokrotnej, zobacz [single Sign-On SAML Protocol](single-sign-on-saml-protocol.md) .
* Zobacz Logowanie jednokrotne [do aplikacji w Azure Active Directory](../manage-apps/what-is-single-sign-on.md) , aby uzyskać więcej informacji na temat różnych sposobów implementacji logowania jednokrotnego w aplikacji.
