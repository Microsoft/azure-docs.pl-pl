---
title: Wybieranie odpowiedniego protokołu federacyjnego dla aplikacji wielodostępnych
description: Wskazówki dla niezależnych dostawców oprogramowania w ramach integracji z usługą Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7dc40d0e4736b4fef8012b08737cbd07bc36bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763299"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Wybieranie odpowiedniego protokołu federacyjnego dla aplikacji wielodostępnej

Podczas tworzenia aplikacji SaaS (Software as a Service) należy wybrać protokół federacyjny, który najlepiej spełnia Twoje potrzeby i klientów. Ta decyzja jest oparta na platformie deweloperskiej i chcemy zintegrować dane z danymi dostępnymi w obrębie klientów pakietu Office 365 i usługi Azure AD.

Zapoznaj się z pełną listą [protokołów dostępnych dla integracji z logowaniem JEDNOkrotnym](what-is-single-sign-on.md) za pomocą Azure Active Directory.
Poniższa tabela zawiera porównanie 
* Uwierzytelnianie Open 2,0 (OAuth 2,0)
* Nawiązywanie połączenia przy otwartym IDENTYFIKATORze (OIDC)
* SAML (SAML)
* Federacja usług sieci Web (WSFed)

| Możliwość| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Logowanie jednokrotne oparte na sieci Web| √| √ |
| Logowanie jednokrotne oparte na sieci Web| √| √ |
| Logowanie jednokrotne oparte na urządzeniach przenośnych| √| √* |
| Logowanie jednokrotne oparte na urządzeniach przenośnych| √| √* |
| Zasady dostępu warunkowego dla aplikacji mobilnych| √| X |
| Bezproblemowe środowisko MFA dla aplikacji mobilnych| √| X |
| Microsoft Graph dostępu| √| X |

* Możliwe, ale firma Microsoft nie udostępnia próbek ani wskazówek.

## <a name="oauth-20-and-open-id-connect"></a>Uwierzytelnianie OAuth 2,0 i łączenie z otwartym IDENTYFIKATORem

Uwierzytelnianie OAuth 2,0 jest [standardowym](https://oauth.net/2/) protokołem do autoryzacji. OIDC (OpenID Connect Connect) to [standardowa](https://openid.net/connect/) warstwa uwierzytelniania tożsamości oparta na protokole OAuth 2,0.

### <a name="benefits"></a>Zalety

Firma Microsoft zaleca używanie OIDC/OAuth 2,0, ponieważ mają one uwierzytelnianie i autoryzację wbudowaną w protokoły. W przypadku protokołu SAML należy dodatkowo zaimplementować autoryzację.

Autoryzacja nieodłączna w tych protokołach umożliwia aplikacji dostęp do i integrowanie z rozbudowanymi danymi użytkownika i organizacji za pomocą interfejsu API Microsoft Graph.

Korzystanie z protokołu OAuth 2,0 i OIDC upraszcza środowisko użytkownika końcowego w przypadku przyjmowania rejestracji Jednokrotnej dla aplikacji. Można łatwo definiować wymagane zestawy uprawnień, które są następnie automatycznie reprezentowane przez administratora lub użytkownika końcowego.

Ponadto użycie tych protokołów umożliwia klientom korzystanie z zasad dostępu warunkowego i uwierzytelniania MFA w celu kontrolowania dostępu do aplikacji. Firma Microsoft udostępnia biblioteki i [przykłady kodu na wielu platformach technologicznych](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) , aby pomóc w rozwoju.  

### <a name="implementation"></a>Implementacja

Możesz zarejestrować swoją aplikację przy użyciu tożsamości firmy Microsoft, która jest dostawcą protokołu OAuth 2,0. Możesz również zarejestrować aplikację opartą na protokole OAuth 2,0 przy użyciu dowolnego innego dostawcy tożsamości, z którym chcesz zintegrować program. 

Aby uzyskać informacje na temat sposobu rejestrowania aplikacji i implementowania tych protokołów dla logowania jednokrotnego do aplikacji sieci Web, zobacz [Autoryzuj dostęp do aplikacji sieci Web za pomocą OpenID Connect Connect i Azure Active Directory](../develop/sample-v2-code.md).  Aby uzyskać informacje na temat sposobu implementowania tych protokołów dla logowania jednokrotnego w aplikacjach mobilnych, zobacz następujące tematy: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Platforma uniwersalna systemu Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 i WSFed

SAML (SAML) jest zwykle używany dla aplikacji sieci Web. Zobacz [, w jaki sposób platforma Azure używa protokołu SAML,](../develop/active-directory-saml-protocol-reference.md) aby zapoznać się z omówieniem. 

Web Services Federation (WSFed) jest [standardem branżowym](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) zwykle używanym przez aplikacje sieci Web, które są opracowywane przy użyciu platformy .NET.

### <a name="benefits"></a>Zalety

SAML 2,0 to wersja najbardziej dojrzałej i większość platform technologicznych obsługuje biblioteki typu open source dla protokołu SAML 2,0. Możesz udostępnić klientom interfejs administracyjny, aby skonfigurować Logowanie jednokrotne SAML. Mogą konfigurować Logowanie jednokrotne SAML dla Microsoft Azure AD i wszystkich innych dostawców tożsamości, które obsługują protokół SAML 2

### <a name="trade-offs"></a>Kompromisy

W przypadku korzystania z protokołów SAML 2,0 lub WSFed dla aplikacji mobilnych niektóre zasady dostępu warunkowego, w tym uwierzytelnianie wieloskładnikowe (MFA), będą mieć obniżoną wydajność. Ponadto, jeśli chcesz uzyskać dostęp do Microsoft Graph, musisz zaimplementować autoryzację za pomocą protokołu OAuth 2,0 w celu wygenerowania niezbędnych tokenów. 

### <a name="implementation"></a>Implementacja

Firma Microsoft nie udostępnia bibliotek dla implementacji języka SAML ani nie zaleca określonych bibliotek. Dostępnych jest wiele bibliotek Open Source.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Logowanie jednokrotne i korzystanie z interfejsu API REST Microsoft Graph 

Microsoft Graph to sieć szkieletowa danych obejmująca wszystkie Microsoft 365, w tym Office 365, Windows 10 i Enterprise Mobility i Security, oraz dodatkowe produkty, takie jak Dynamics 365. Obejmuje to podstawowe schematy jednostek, takich jak użytkownicy, grupy, kalendarz, poczta, pliki i inne, które zwiększają produktywność użytkowników. Microsoft Graph oferuje trzy interfejsy dla deweloperów, interfejs API oparty na usłudze REST, Microsoft Graph połączenia danych oraz łączniki, które umożliwiają deweloperom Dodawanie własnych danych do Microsoft Graph.  

Użycie któregokolwiek z powyższych protokołów logowania jednokrotnego umożliwia aplikacji dostęp do bogatych danych dostępnych za pośrednictwem interfejsu API REST Microsoft Graph. Dzięki temu klienci mogą uzyskać większą wartość z inwestycji w Microsoft 365. Na przykład aplikacja może wywoływać interfejs API Microsoft Graph, aby zintegrować go z wystąpieniem pakietu Office 365 i Microsoft Officeą użytkowników powierzchniowych i elementami programu SharePoint w aplikacji. 

Jeśli używasz usługi Open ID Connect do uwierzytelniania, środowisko programistyczne jest bezproblemowe, ponieważ będziesz używać OAuth2, podstawą połączenia typu Open ID, aby uzyskać tokeny do wywoływania Microsoft Graph interfejsów API. Jeśli aplikacja używa protokołu SAML lub WSFed, należy dodać dodatkowy kod w aplikacji, aby uzyskać te OAuth2 w celu uzyskania tokenów wymaganych do wywoływania Microsoft Graph interfejsów API. 

## <a name="next-steps"></a>Następne kroki

[Włączanie logowania jednokrotnego dla aplikacji wielodostępnej](isv-sso-content.md)

[Tworzenie dokumentacji dla aplikacji wielodostępnej](isv-create-sso-documentation.md)
