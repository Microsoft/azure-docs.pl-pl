---
title: Obsługa AD FS w MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat obsługi Active Directory Federation Services (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: a146b310e6056954ac2655ff2fd99e1e3d7c694f
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063641"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Obsługa Active Directory Federation Services w MSAL.NET
Active Directory Federation Services (AD FS) w systemie Windows Server umożliwia dodawanie uwierzytelniania i autoryzacji na podstawie OpenID Connect Connect i OAuth 2,0 na potrzeby aplikacji, które tworzysz. Aplikacje te mogą następnie uwierzytelniać użytkowników bezpośrednio przed AD FS. Aby uzyskać więcej informacji, Przeczytaj [AD FS scenariusze dla deweloperów](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Biblioteka Microsoft Authentication Library for .NET (MSAL.NET) obsługuje dwa scenariusze uwierzytelniania na AD FS:

- MSAL.NET rozmowy do Azure Active Directory, który sam jest *federacyjny* z AD FS.
- MSAL.NET się **bezpośrednio** do urzędu usług AD FS. Jest to obsługiwane tylko w AD FS 2019 i nowszych. Jednym z scenariuszy, w których ten wyróżniono, jest pomoc techniczna [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL nawiązuje połączenie z usługą Azure AD, która jest federacyjnym z AD FS
MSAL.NET obsługuje łączenie z usługą Azure AD, co umożliwia użytkownikom zarządzanym (zarządzani przez użytkowników w usłudze Azure AD) lub użytkownikom federacyjnym (Użytkownicy zarządzani przez innego dostawcę tożsamości, takie jak AD FS). MSAL.NET nie wie o faktach, że użytkownicy są federacyjnymi. W zakresie, w jakim jest to konieczne, komunikuje się z usługą Azure AD.

[Urząd](msal-client-application-configuration.md#authority) , którego używasz w tym przypadku, jest zazwyczaj urzędem certyfikacji (nazwa hosta urzędu + dzierżawca, typowe lub organizacje).

### <a name="acquiring-a-token-interactively"></a>Interaktywny uzyskiwanie tokenu
Po wywołaniu `AcquireTokenInteractive` metody, środowisko użytkownika jest zwykle:

1. Użytkownik wprowadza swój identyfikator konta.
2. Usługa Azure AD wyświetla krótko komunikat "przejmowanie strony Twojej organizacji".
3. Użytkownik zostanie przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowana do logo organizacji.

Obsługiwane wersje AD FS w tym scenariuszu federacyjnym to AD FS v2, AD FS v3 (Windows Server 2012 R2) i AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Uzyskiwanie tokenu przy użyciu AcquireTokenByIntegratedAuthentication lub AcquireTokenByUsernamePassword
Podczas uzyskiwania tokenu przy użyciu `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` metod lub MSAL.NET pobiera dostawcę tożsamości do kontaktu na podstawie nazwy użytkownika.  MSAL.NET odbiera [token SAML 1,1](reference-saml-tokens.md) po skontaktowaniu się z dostawcą tożsamości.  MSAL.NET następnie dostarcza token SAML do usługi Azure AD jako potwierdzenie użytkownika (podobnie jak w przypadku [przepływu w imieniu](msal-authentication-flows.md#on-behalf-of)) w celu uzyskania tokenu JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL łączy się bezpośrednio z AD FS
MSAL.NET obsługuje nawiązywanie połączenia z AD FS 2019, który jest zgodny z IDENTYFIKATORem Open Connect i rozumie PKCE i zakresy. Ta obsługa wymaga, aby dodatek Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) został zastosowany do systemu Windows Server. W przypadku nawiązywania bezpośredniego połączenia z AD FS urząd, który ma być używany do kompilowania aplikacji, jest podobny do `https://mysite.contoso.com/adfs/` .

Obecnie nie ma żadnych planów do obsługi bezpośredniego połączenia z:

- AD FS 16, ponieważ nie obsługuje PKCE i nadal używa zasobów, a nie zakresu
- AD FS v2, który nie jest zgodny z OIDC.

 Jeśli musisz obsługiwać scenariusze wymagające bezpośredniego połączenia z AD FS 2016, użyj najnowszej wersji [biblioteki uwierzytelniania Azure Active Directory](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Po uaktualnieniu systemu lokalnego do AD FS 2019 będzie możliwe użycie MSAL.NET.

## <a name="next-steps"></a>Następne kroki

W przypadku federacyjnego przypadku należy zapoznać się z tematem [Konfigurowanie zachowania logowania Azure Active Directory aplikacji przy użyciu zasad odnajdywania obszaru głównego](../manage-apps/configure-authentication-for-federated-users-portal.md) .