---
title: Azure Active Directory Omówienie protokołu uwierzytelniania i synchronizacji
description: Wskazówki dotyczące architektury integrowania usługi Azure AD ze starszymi protokołami uwierzytelniania i wzorcami synchronizacji
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441202"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory integracje z protokołami uwierzytelniania i synchronizacji

Microsoft Azure Active Directory (Azure AD) umożliwia integrację z wieloma protokołami uwierzytelniania i synchronizacji. Integracja z uwierzytelnianiem umożliwia korzystanie z usługi Azure AD oraz jej funkcji zabezpieczeń i zarządzania bez wprowadzania zmian w aplikacjach korzystających ze starszych metod uwierzytelniania. W ramach integracji synchronizacji można synchronizować dane użytkowników i grup w usłudze Azure AD, a następnie korzystać z funkcji zarządzania usługą Azure AD. Niektóre wzorce synchronizacji umożliwiają również automatyczną obsługę administracyjną.

## <a name="legacy-authentication-protocols"></a>Starsze protokoły uwierzytelniania

W poniższej tabeli przedstawiono uwierzytelnianie w usłudze Azure AD przy użyciu starszych protokołów uwierzytelniania i ich możliwości. Wybierz nazwę protokołu uwierzytelniania do sprawdzenia

* Szczegółowy opis

* Zastosowanie

* Diagram architektoniczny

* Wyjaśnienie składników systemu

* Linki do wdrożenia integracji

 

| Protokół uwierzytelniania| Authentication| Autoryzacja| Multi-Factor Authentication| Dostęp warunkowy |
| - |- | - | - | - |
| [Uwierzytelnianie na podstawie nagłówków](auth-header-based.md)|![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie LDAP](auth-ldap.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| | |  |
| [Uwierzytelnianie OAuth 2.0](auth-oauth2.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie OIDC](auth-oidc.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie Logowanie jednokrotne na podstawie hasła](auth-password-based-sso.md )| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie za pomocą protokołu RADIUS]( auth-radius.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| | ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Pulpit zdalny usług bramy](auth-remote-desktop-gateway.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Secure Shell (SSH)](auth-ssh.md) |  ![znacznik wyboru](./media/authentication-patterns/check.png)| | ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie SAML](auth-saml.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |
| [Uwierzytelnianie systemu Windows — ograniczone delegowanie protokołu Kerberos](auth-kcd.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Wzorce synchronizacji

W poniższej tabeli przedstawiono integrację usługi Azure AD z wzorcem synchronizacji i ich możliwościami. Wybierz nazwę wzorca, aby zobaczyć

* Szczegółowy opis

* Zastosowanie

* Diagram architektoniczny

* Wyjaśnienie składników systemu

* Linki do wdrożenia integracji



| Wzorzec synchronizacji| Synchronizacja katalogów| Aprowizowanie użytkowników |
| - | - | - |
| [Synchronizacja katalogów](sync-directory.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)|  |
| [Synchronizacja LDAP](sync-ldap.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)|  |
| [Synchronizacja SCIM](sync-scim.md)| ![znacznik wyboru](./media/authentication-patterns/check.png)| ![znacznik wyboru](./media/authentication-patterns/check.png) |

