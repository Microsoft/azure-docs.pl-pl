---
title: Azure Active Directory Omówienie protokołu uwierzytelniania i synchronizacji
description: Wskazówki dotyczące architektury dotyczące osiągania tego wzorca uwierzytelniania
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114500"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Azure Active Directory integracje z starszymi protokołami uwierzytelniania i synchronizacji

Microsoft Azure Active Directory (Azure AD) umożliwia integrację z wieloma protokołami uwierzytelniania i synchronizacji. Integracja z uwierzytelnianiem umożliwia korzystanie z usługi Azure AD oraz jej funkcji zabezpieczeń i zarządzania bez wprowadzania zmian w aplikacjach korzystających ze starszych metod uwierzytelniania. W ramach integracji synchronizacji można synchronizować dane użytkowników i grup w usłudze Azure AD, a następnie korzystać z funkcji zarządzania usługą Azure AD. Niektóre wzorce synchronizacji umożliwiają również automatyczną obsługę administracyjną.

## <a name="authentication-patterns"></a>Wzorce uwierzytelniania

W poniższej tabeli przedstawiono wzorce uwierzytelniania i ich możliwości. Wybierz nazwę wzorców uwierzytelniania do wyświetlenia

* Szczegółowy opis

* Zastosowanie

* Diagram architektoniczny

* Wyjaśnienie składników systemu

* Linki do wdrożenia integracji

 

| Wzorce uwierzytelniania| Authentication| Autoryzacja| Multi-Factor Authentication| Dostęp warunkowy |
| - |- | - | - | - |
| [Uwierzytelnianie oparte na nagłówkach](auth-header-based.md)|![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie LDAP](auth-ldap.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| | |  |
| [Uwierzytelnianie OAuth 2,0](auth-oauth2.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie OIDC](auth-oidc.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie Logowanie jednokrotne na podstawie hasła](auth-password-based-sso.md )| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie za pomocą protokołu RADIUS]( auth-radius.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| | ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Pulpit zdalny usług bramy](auth-remote-desktop-gateway.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie SAML](auth-saml.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie systemu Windows — ograniczone delegowanie protokołu Kerberos](auth-kcd.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Wzorce synchronizacji

W poniższej tabeli przedstawiono wzorce synchronizacji i ich możliwości. Wybierz nazwę wzorca, aby zobaczyć

* Szczegółowy opis

* Zastosowanie

* Diagram architektoniczny

* Wyjaśnienie składników systemu

* Linki do wdrożenia integracji



| Wzorzec synchronizacji| Synchronizacja katalogów| Aprowizowanie użytkowników |
| - | - | - |
| [Synchronizacja katalogów](sync-directory.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)|  |
| [Synchronizacja LDAP](sync-ldap.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)|  |
| [Standard scim synchronizacji](sync-scim.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |

