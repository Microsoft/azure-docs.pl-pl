---
title: Scenariusz wymiany tokenów platformy tożsamości firmy Microsoft z użyciem protokołu SAML i OIDC/OAuth w Azure Active Directory
description: Zapoznaj się z typowymi scenariuszami wymiany tokenów podczas pracy z protokołami SAML i OIDC/OAuth w Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 812bf7bd68362667fcd1b636d0d28fdbb21c1409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582353"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Scenariusze wymiany tokenów platformy tożsamości firmy Microsoft z użyciem protokołu SAML i OIDC/OAuth

Protokoły SAML i OpenID Connect Connect (OIDC)/OAuth są popularnymi protokołami używanymi do implementowania jednego Sign-On (SSO). Niektóre aplikacje mogą implementować tylko protokół SAML, a inne mogą implementować tylko OIDC/OAuth. Oba protokoły używają tokenów do przekazywania kluczy tajnych. Aby dowiedzieć się więcej na temat protokołu SAML, zobacz [Single Sign-On SAML Protocol](single-sign-on-saml-protocol.md). Aby dowiedzieć się więcej na temat OIDC/OAuth, zobacz [protokoły uwierzytelniania oauth 2,0 i OpenID Connect na platformie tożsamości firmy Microsoft](active-directory-v2-protocols.md).

W tym artykule opisano typowy scenariusz, w którym aplikacja implementuje język SAML, ale wywołuje interfejs API programu Graph, która używa OIDC/OAuth. Dla osób pracujących nad tym scenariuszem są udostępniane podstawowe wskazówki.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Scenariusz: masz token SAML i chcesz wywołać interfejs API programu Graph
Wiele aplikacji jest implementowanych przy użyciu protokołu SAML. Jednak interfejs API programu Graph używa protokołów OIDC/OAuth. Aby dodać funkcje OIDC/OAuth do aplikacji SAML, istnieje możliwość, że nie jest prosta. Gdy w aplikacji jest dostępna funkcja OAuth, interfejs API programu Graph może być używana.

Ogólna strategia polega na dodaniu stosu OIDC/OAuth do aplikacji. W przypadku aplikacji, która implementuje oba standardy, można użyć pliku cookie sesji. Nie można jawnie wymieniać tokenu. Rejestrujesz użytkownika w usłudze przy użyciu języka SAML, który generuje plik cookie sesji. Gdy interfejs API programu Graph wywołuje przepływ OAuth, do uwierzytelnienia służy plik cookie sesji. Ta strategia zakłada, że testy dostępu warunkowego są przekazywane i użytkownik jest autoryzowany.

> [!NOTE]
> Zalecana Biblioteka do dodawania zachowań OIDC/OAuth to biblioteka uwierzytelniania firmy Microsoft (MSAL). Aby dowiedzieć się więcej na temat MSAL, zobacz [Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)](msal-overview.md). Poprzednia biblioteka została wywołana Active Directory Authentication Library (ADAL), ale nie jest to zalecane, ponieważ MSAL zastępuje ją.

## <a name="next-steps"></a>Następne kroki
- [Przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md)
