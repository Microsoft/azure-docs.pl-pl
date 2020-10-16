---
title: Uwierzytelnianie OAUTH 2,0 z Azure Active Directory
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
ms.openlocfilehash: ea22c4e5b363eaa3ecc2a736dfef714666310062
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114371"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Uwierzytelnianie OAuth 2,0 z Azure Active Directory

Uwierzytelnianie OAuth 2,0 jest protokołem branżowym do autoryzacji. Umożliwia użytkownikowi udzielenie ograniczonego dostępu do chronionych zasobów. Zaprojektowana do pracy z funkcją protokołu HTTP (Hypertext Transfer Protocol) — uwierzytelnianie OAuth oddziela rolę klienta od właściciela zasobu. Klient żąda dostępu do zasobów kontrolowanych przez właściciela zasobu i hostowanych przez serwer zasobów. Serwer zasobów wystawia tokeny dostępu z zatwierdzeniem właściciela zasobu. Klient uzyskuje dostęp do chronionych zasobów hostowanych przez serwer zasobów przy użyciu tokenów dostępu. 

Protokół OAuth 2,0 jest bezpośrednio związany z OpenID Connect Connect (OIDC). Ponieważ OIDC to warstwa uwierzytelniania i autoryzacji utworzona na podstawie protokołu OAuth 2,0, nie jest wstecznie zgodna z uwierzytelnianiem OAuth 1,0. Azure Active Directory (Azure AD) obsługuje wszystkie przepływy protokołu OAuth 2,0. 

## <a name="use-when"></a>Zastosowania:

W przypadku rozbudowanego klienta & nowoczesne scenariusze aplikacji i dostęp do interfejsu API sieci Web RESTful.

![Diagram architektury](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Składniki systemu

* **Użytkownik**: żąda usługi z aplikacji sieci Web (aplikacja). Użytkownik jest zazwyczaj właścicielem zasobu, który jest właścicielem danych i ma możliwość zezwalania klientom na dostęp do danych lub zasobów. 

* **Przeglądarka sieci Web**: przeglądarka sieci Web, z którą współdziała użytkownik, to klient uwierzytelniania OAuth. 

* **Aplikacja internetowa**: aplikacja internetowa lub serwer zasobów, w której znajdują się zasoby lub dane. Ufa serwerowi autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta protokołu OAuth. 

* **Azure AD**: usługa Azure AD jest serwerem autoryzacji, znanym również jako dostawca tożsamości (dostawcy tożsamości). W pełni obsługuje ona wszystkie czynności do wykonania z informacjami o użytkowniku, ich dostępem i relacją zaufania. Jest on odpowiedzialny za wydawanie tokenów udzielających i odwoływania dostępu do zasobów.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementowanie uwierzytelniania OAuth 2,0 przy użyciu usługi Azure AD

* [Integrowanie aplikacji z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Protokoły uwierzytelniania OAuth 2,0 i OpenID Connect na platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Typy aplikacji i OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
