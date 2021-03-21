---
title: Obsługa AD FS (MSAL for Java)
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat obsługi Active Directory Federation Services (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla języka Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 82e3f3b32c6d96b83ec1d0402f344e7d65788c06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063709"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Obsługa Active Directory Federation Services w programie MSAL for Java

Active Directory Federation Services (AD FS) w systemie Windows Server umożliwia dodawanie uwierzytelniania i autoryzacji na podstawie OpenID Connect Connect i OAuth 2,0 do biblioteki uwierzytelniania firmy Microsoft dla języka Java (MSAL for Java). Po zintegrowaniu aplikacja może uwierzytelniać użytkowników w AD FS federacyjnych za pomocą usługi Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [AD FS scenariusze dla deweloperów](/windows-server/identity/ad-fs/ad-fs-development).

Aplikacja, która używa MSAL for Java, będzie komunikować się z Azure Active Directory (Azure AD), a następnie tworzy federację do AD FS.

MSAL for Java nawiązuje połączenie z usługą Azure AD, co oznacza, że użytkownicy, którzy są zarządzani za pomocą usługi Azure AD (zarządzani użytkownicy) lub Użytkownicy zarządzani przez innego dostawcę tożsamości, na przykład AD FS (użytkowników federacyjnych). MSAL for Java nie wie, że użytkownik jest federacyjny. Po prostu rozmowy z usługą Azure AD.

[Urząd](msal-client-application-configuration.md#authority) , którego używasz w tym przypadku, jest zazwyczaj urzędem certyfikacji (nazwa hosta urzędu + dzierżawca, typowe lub organizacje).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktywny pozyskiwanie tokenu dla użytkownika federacyjnego

Podczas wywoływania `ConfidentialClientApplication.AcquireToken()` lub `PublicClientApplication.AcquireToken()` z `AuthorizationCodeParameters` lub `DeviceCodeParameters` , środowisko użytkownika jest zwykle:

1. Użytkownik wprowadza swój identyfikator konta.
2. Usługa Azure AD krótko wyświetli "przejście do strony organizacji" i użytkownik zostanie przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowana do logo organizacji.

Obsługiwane wersje AD FS w tym scenariuszu federacyjnym są następujące:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Uzyskiwanie tokenu za pomocą nazwy użytkownika i hasła

Po pozyskaniu tokenu przy użyciu programu `ConfidentialClientApplication.AcquireToken()` lub `PublicClientApplication.AcquireToken()` z `IntegratedWindowsAuthenticationParameters` lub `UsernamePasswordParameters` , MSAL for Java pobiera dostawcę tożsamości do kontaktu na podstawie nazwy użytkownika. MSAL for Java pobiera token [tokenu SAML 1,1](reference-saml-tokens.md) od dostawcy tożsamości, który następnie udostępnia usługę Azure AD, która zwraca token sieci Web JSON (JWT).

## <a name="next-steps"></a>Następne kroki

W przypadku federacyjnego przypadku należy zapoznać się z tematem [Konfigurowanie zachowania logowania Azure Active Directory aplikacji przy użyciu zasad odnajdywania obszaru głównego](../manage-apps/configure-authentication-for-federated-users-portal.md) .