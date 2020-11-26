---
title: OpenID Connect Połącz uwierzytelnianie z Azure Active Directory
description: Wskazówki dotyczące architektury w zakresie osiągnięcia uwierzytelniania OpenID Connect Connect przy użyciu Azure Active Directory.
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
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168665"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect Połącz uwierzytelnianie z Azure Active Directory

OpenID Connect Connect (OIDC) to protokół uwierzytelniania oparty na protokole OAuth2 (używanym do autoryzacji). OIDC używa znormalizowanych przepływów komunikatów z OAuth2 w celu świadczenia usług tożsamości. 

Celem projektowania OIDC jest "ułatwianie prostych i skomplikowanych elementów". OIDC umożliwia deweloperom uwierzytelnianie użytkowników w witrynach sieci Web i aplikacjach bez konieczności posiadania własnych plików haseł i zarządzania nimi. Zapewnia to programowi App Builder bezpieczny sposób weryfikowania tożsamości osoby aktualnie używającej przeglądarki lub natywnej aplikacji połączonej z aplikacją.

Uwierzytelnianie użytkownika musi odbywać się u dostawcy tożsamości, w którym zostanie sprawdzona sesja lub poświadczenia użytkownika. Aby to zrobić, potrzebny jest zaufany Agent. Aplikacje natywne zwykle uruchamiają w tym celu przeglądarkę systemową. Widoki osadzone są uważane za niezaufane, ponieważ nie ma nic, aby uniemożliwić śledzenie aplikacji przy użyciu hasła użytkownika. 

Oprócz uwierzytelniania użytkownik może zostać poproszony o zgodę. Zgoda to jawne uprawnienie użytkownika umożliwiające aplikacji dostęp do chronionych zasobów. Zgoda różni się od uwierzytelniania, ponieważ dla zasobu należy podać tylko jeden raz. Wyrażanie zgody jest ważne, dopóki użytkownik lub administrator ręcznie odwoła przyznanie dotacji. 

## <a name="use-when"></a>Zastosowania

Istnieje potrzeba zgody użytkownika i logowania do sieci Web.

![Diagram architektoniczny](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: żąda usługi z aplikacji.

* **Zaufany Agent**: składnik, z którym użytkownik prowadzi interakcję. Ten zaufany Agent jest zazwyczaj przeglądarką sieci Web.

* **Aplikacja**: aplikacja lub serwer zasobów jest miejscem, w którym znajduje się zasób lub dane. Ufa dostawcy tożsamości do bezpiecznego uwierzytelniania i autoryzowania zaufanego agenta. 

* **Azure AD**: dostawca OIDC, znany także jako dostawca tożsamości, bezpiecznie zarządza wszelkimi informacjami o użytkowniku, ich dostępie i relacjach zaufania między stronami w przepływie. Uwierzytelnia on tożsamość użytkownika, przyznaje i odwołuje dostęp do zasobów oraz wystawia tokeny. 

## <a name="implement-oidc-with-azure-ad"></a>Implementowanie OIDC przy użyciu usługi Azure AD

* [Integrowanie aplikacji z usługą Azure AD](../saas-apps/tutorial-list.md) 

* [Protokoły uwierzytelniania OAuth 2,0 i OpenID Connect na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-protocols.md) 

* [Microsoft Identity platform i OpenID Connect Connect Protocol](../develop/v2-protocols-oidc.md) 

* [Logowanie w sieci Web za pomocą OpenID Connect Connect in Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Zabezpieczanie aplikacji za pomocą warstwy OpenID Connect i usługi Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

