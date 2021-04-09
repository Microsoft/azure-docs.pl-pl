---
title: Pomoc techniczna dotycząca platformy Azure AD FS (MSAL Python)
titleSuffix: Microsoft identity platform
description: Informacje na temat obsługi Active Directory Federation Services (AD FS) w bibliotece uwierzytelniania firmy Microsoft dla języka Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d847e38a6114b0e5f8d56f6e994aa47dfbe929e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583709"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Obsługa Active Directory Federation Services w programie MSAL for Python

Active Directory Federation Services (AD FS) w systemie Windows Server umożliwia dodawanie uwierzytelniania i autoryzacji usługi OpenID Connect Connect i OAuth 2,0 do aplikacji przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL) dla języka Python. Za pomocą MSAL dla biblioteki języka Python aplikacja może uwierzytelniać użytkowników bezpośrednio przed AD FS. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [AD FS scenariusze dla deweloperów](/windows-server/identity/ad-fs/ad-fs-development).

Istnieją zwykle dwa sposoby uwierzytelniania w odniesieniu do AD FS:

- MSAL Język Python rozmowy do Azure Active Directory, który sam jest federacyjny z innymi dostawcami tożsamości. Federacja odbywa się za pomocą AD FS. MSAL Python nawiązuje połączenie z usługą Azure AD, co oznacza, że użytkownicy, którzy są zarządzani za pomocą usługi Azure AD (zarządzani użytkownicy) lub Użytkownicy zarządzani przez innego dostawcę tożsamości, takie jak AD FS (Użytkownicy zafederacyjnych). MSAL Python nie wie, że użytkownik jest federacyjny. Po prostu rozmowy z usługą Azure AD. [Urząd](msal-client-application-configuration.md#authority) , którego używasz w tym przypadku, jest zazwyczaj urzędem certyfikacji (nazwa hosta urzędu + dzierżawca, typowe lub organizacje).
- MSAL Python bezpośrednio do urzędu AD FS. Jest to obsługiwane tylko przez AD FS 2019 i nowsze.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Nawiązywanie połączenia z usługą Active Directory Federation with AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktywny pozyskiwanie tokenu dla użytkownika federacyjnego

Poniżej można nawiązać bezpośrednie połączenie z Active Directory Federation Services (AD FS) lub Active Directory.

Podczas wywoływania `acquire_token_by_authorization_code` lub `acquire_token_by_device_flow` środowisko użytkownika jest zwykle następujące:

1. Użytkownik wprowadza swój identyfikator konta.
2. Usługa Azure AD wyświetla krótko komunikat "przejmowanie strony organizacji", a użytkownik zostaje przekierowany do strony logowania dostawcy tożsamości. Strona logowania jest zwykle dostosowana do logo organizacji.

Obsługiwane wersje AD FS w tym scenariuszu federacyjnym są następujące:
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Uzyskiwanie tokenu za pomocą nazwy użytkownika i hasła

Poniżej można nawiązać bezpośrednie połączenie z Active Directory Federation Services (AD FS) lub Active Directory.

Po pozyskaniu tokenu przy użyciu programu `acquire_token_by_username_password` MSAL Python pobiera dostawcę tożsamości do kontaktu na podstawie nazwy użytkownika. MSAL Python pobiera [token SAML 1,1](reference-saml-tokens.md) od dostawcy tożsamości, który następnie zapewnia usłudze Azure AD, która zwraca token sieci Web JSON (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Bezpośrednie łączenie z AD FS

Po nawiązaniu połączenia z usługą AD FS urząd, który ma być używany do kompilowania aplikacji, będzie podobny `https://somesite.contoso.com/adfs/`

MSAL Python obsługuje usługi ADFS 2019.

Nie obsługuje bezpośredniego połączenia z usługami ADFS 2016 i ADFS v2. Aby obsługiwać scenariusze wymagające bezpośredniego połączenia z usługami ADFS 2016, użyj najnowszej wersji biblioteki ADAL Python. Po uaktualnieniu systemu lokalnego do usług AD FS 2019 można użyć języka Python MSAL.

## <a name="next-steps"></a>Następne kroki

- W przypadku federacyjnego przypadku należy zapoznać się z tematem [Konfigurowanie zachowania logowania Azure Active Directory aplikacji przy użyciu zasad odnajdywania obszaru głównego](../manage-apps/configure-authentication-for-federated-users-portal.md) .
