---
title: Azure Active Directory dla deweloperów (v 1.0) — Omówienie
description: Ten artykuł zawiera omówienie logowania się na kontach służbowych firmy Microsoft przy użyciu punktu końcowego Azure Active Directory v 1.0 i platformy.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8cb426239fe9f3dea207df06b6f2bafbdb54058a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88117672"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Azure Active Directory dla deweloperów (v 1.0) — Omówienie

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) to usługa zarządzania tożsamościami w chmurze, która umożliwia deweloperom tworzenie aplikacji zapewniających bezpieczne logowanie użytkowników mających konta służbowe Microsoft. Usługa Azure AD obsługuje zarówno deweloperów tworzących aplikacje biznesowe dla pojedynczej dzierżawy, jak i deweloperów chcących tworzyć aplikacje wielodostępne. Oprócz podstawowego logowania usługa Azure AD umożliwia również aplikacjom wywoływanie interfejsów API firmy Microsoft, takich jak [Microsoft Graph](/graph/overview), a także niestandardowych interfejsów API opartych na platformie Azure AD. W tej dokumentacji pokazano, jak dodać obsługę usługi Azure AD do własnej aplikacji, używając standardowych w branży protokołów, takich jak OAuth 2.0 i OpenID Connect.

> [!NOTE]
> Większość zawartości na tej stronie koncentruje się na punkcie końcowym i platformie v 1.0, który obsługuje tylko konta służbowe firmy Microsoft. Jeśli chcesz zalogować się do użytkowników lub osobistych kont Microsoft, zapoznaj się z informacjami w [punkcie końcowym i platformie v 2.0](../develop/v2-overview.md). Punkt końcowy v 2.0 oferuje ujednolicone środowisko programistyczne dla aplikacji, które chcą zalogować się do wszystkich tożsamości firmy Microsoft.

- [Podstawowe informacje o uwierzytelnianiu](v1-authentication-scenarios.md) Wprowadzenie do uwierzytelniania za pomocą usługi Azure AD.
- [Typy aplikacji](app-types.md) Omówienie scenariuszy uwierzytelniania obsługiwanych przez usługę Azure AD.

## <a name="get-started"></a>Rozpoczęcie pracy

Przewodniki Szybki Start i samouczki dotyczące programu v 1.0 przeprowadzą Cię przez proces tworzenia aplikacji na preferowanej platformie przy użyciu zestawu SDK biblioteki Azure AD Authentication Library (ADAL). Aby rozpocząć pracę, zobacz **samouczki** **Szybki Start** i wersja 1.0 usługi [Microsoft Identity platform (Azure Active Directory dla deweloperów)](index.yml) .

## <a name="how-to-guides"></a>Przewodniki z instrukcjami

Zapoznaj się z przewodnikami instruktażowymi dotyczącymi **wersji 1.0** , aby uzyskać szczegółowe informacje i wskazówki dotyczące najbardziej typowych zadań w usłudze Azure AD.

## <a name="reference-topics"></a>Tematy informacyjne

Następujące artykuły zawierają szczegółowe informacje o interfejsach API, komunikatach protokołów i terminach używanych w usłudze Azure AD.

- [Biblioteki uwierzytelniania (ADAL)](active-directory-authentication-libraries.md) Omówienie bibliotek i zestawów SDK dostarczanych przez usługę Azure AD.
- [Przykłady kodu](sample-v1-code.md) Lista wszystkich przykładów kodu usługi Azure AD.
- [Słownik](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Terminologia i definicje słów używanych w tej dokumentacji.

## <a name="videos"></a>Filmy wideo

Zapoznaj się z tematem [Azure Active Directory deweloperskich filmów wideo](videos.md) , aby uzyskać pomoc w migracji do nowej platformy tożsamości firmy Microsoft.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]