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
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584300"
---
# <a name="authentication-vs-authorization"></a>Uwierzytelnianie a autoryzacja

Ten artykuł definiuje uwierzytelnianie i autoryzację oraz krótko opisuje, jak można używać platformy tożsamości firmy Microsoft do uwierzytelniania i autoryzowania użytkowników w aplikacjach sieci Web, interfejsach API sieci Web lub aplikacjach wywołujących chronione interfejsy API sieci Web. Jeśli zobaczysz termin, którego nie znasz, wypróbuj nasz [słownik](developer-glossary.md) lub nasze [wideo platformy Microsoft Identity platform](identity-videos.md) , które obejmują podstawowe pojęcia.

## <a name="authentication"></a>Authentication

**Uwierzytelnianie** to proces potwierdzania użytkownika. Uwierzytelnianie jest czasami określane terminem AuthN. Platforma tożsamości firmy Microsoft implementuje protokół [Connect OpenID Connect](https://openid.net/connect/) do obsługi uwierzytelniania.

## <a name="authorization"></a>Autoryzacja

**Autoryzacja** to czynność udzielenia uprawnienia uwierzytelnionej stronie. Określa dane, do których można uzyskać dostęp i co można zrobić z tymi danymi. Autoryzacja jest czasami określana terminem AuthZ. Platforma tożsamości firmy Microsoft implementuje protokół [OAuth 2,0](https://oauth.net/2/) do obsługi autoryzacji.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Uwierzytelnianie i autoryzacja przy użyciu platformy tożsamości firmy Microsoft

Zamiast tworzyć aplikacje, które zachowują własne informacje o nazwie użytkownika i hasła, co wiąże się z dużym obciążeniem administracyjnym, gdy trzeba dodać lub usunąć użytkowników w wielu aplikacjach, aplikacje mogą delegować tę odpowiedzialność do scentralizowanego dostawcy tożsamości.

Azure Active Directory (Azure AD) to scentralizowany dostawca tożsamości w chmurze. Delegowanie uwierzytelniania i autoryzacji pozwala na takie scenariusze, jak zasady dostępu warunkowego, które wymagają, aby użytkownik znajdował się w określonej lokalizacji, korzystać z uwierzytelniania wieloskładnikowego, a także umożliwić użytkownikowi logowanie się jednokrotne do wszystkich aplikacji sieci Web, które współużytkują ten sam katalog scentralizowany. Ta funkcja jest określana jako logowanie jednokrotne **(SSO)**.

Platforma tożsamości firmy Microsoft upraszcza uwierzytelnianie i autoryzację dla deweloperów aplikacji, zapewniając tożsamość jako usługę, z obsługą standardowych protokołów, takich jak OAuth 2,0 i OpenID Connect Connect, a także bibliotek typu "open source" dla różnych platform, które ułatwiają szybkie rozpoczęcie tworzenia kodu. Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują się do wszystkich tożsamości firmy Microsoft, uzyskują tokeny, aby wywoływać [Microsoft Graph](https://developer.microsoft.com/graph/), inne interfejsy API firmy Microsoft lub interfejsy API zaprojektowane przez deweloperów. Aby uzyskać więcej informacji, zobacz [ewolucja platformy tożsamości firmy Microsoft](about-microsoft-identity-platform.md).

## <a name="next-steps"></a>Następne kroki

W przypadku innych tematów obejmujących podstawowe informacje dotyczące uwierzytelniania i autoryzacji:

* Zobacz [tokeny zabezpieczające](security-tokens.md) , aby dowiedzieć się, jak tokeny dostępu, tokeny odświeżania i tokeny identyfikatorów są używane podczas uwierzytelniania i autoryzacji.
* Zobacz [model aplikacji](application-model.md) , aby dowiedzieć się więcej o procesie rejestracji aplikacji, aby umożliwić integrację z platformą tożsamości firmy Microsoft.
* Aby dowiedzieć się więcej o przepływie logowania aplikacji sieci Web, komputerów stacjonarnych i aplikacji mobilnych na platformie tożsamości firmy Microsoft, zobacz temat rejestrowanie w usłudze [Flow](app-sign-in-flow.md) .

Aby dowiedzieć się więcej na temat protokołów implementowanych przez platformę tożsamości firmy Microsoft:

* Aby uzyskać więcej informacji na temat standardów OpenID Connect Connect i OAuth 2,0, zobacz [Protokoły OAuth 2,0 i OpenID Connect Connect na platformie tożsamości firmy Microsoft](active-directory-v2-protocols.md) .
* Zobacz [Protokół SAML logowania](single-sign-on-saml-protocol.md) jednokrotnego, aby uzyskać więcej informacji na temat obsługi rejestracji jednokrotnej przez platformę Microsoft Identity.
