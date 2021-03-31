---
title: Co to jest logowanie jednokrotne na platformie Azure?
description: Dowiedz się, jak działa Logowanie jednokrotne (SSO) przy użyciu Azure Active Directory. Użyj logowania jednokrotnego, aby użytkownicy nie musieli zapamiętywać haseł dla każdej aplikacji. Aby uprościć administrowanie zarządzaniem kontami, należy również użyć logowania jednokrotnego.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 36025a5a68e4c3d0de1d6b11a13df656c72745d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260201"
---
# <a name="what-is-single-sign-on-sso"></a>Co to jest logowanie jednokrotne?

Logowanie jednokrotne oznacza, że użytkownik nie musi logować się do każdej używanej aplikacji. Użytkownik loguje się raz, a poświadczenia są również używane dla innych aplikacji.

Jeśli jesteś użytkownikiem końcowym, możesz nie zadbać o szczegóły dotyczące rejestracji jednokrotnej. Warto tylko korzystać z aplikacji, które zwiększają produktywność, bez konieczności pisania hasła. Aplikacje można znaleźć pod adresem: https://myapps.microsoft.com .
 
Jeśli jesteś administratorem lub informatykiem, przeczytaj artykuł, aby dowiedzieć się więcej o rejestracji jednokrotnej i sposobie jej implementacji na platformie Azure.

## <a name="single-sign-on-basics"></a>Podstawy logowania jednokrotnego
Logowanie jednokrotne zapewnia bardzo dużeą w przód w celu zalogowania się i używania aplikacji przez użytkowników. Systemy uwierzytelniania oparte na rejestracji jednokrotnej są często nazywane "nowoczesnym uwierzytelnianiem". Nowoczesne uwierzytelnianie i logowanie jednokrotne należy do kategorii obliczeń o nazwie Zarządzanie tożsamościami i dostępem (IAM). Aby dowiedzieć się, jak to zrobić przy użyciu logowania jednokrotnego, zapoznaj się z tym wideo.

Podstawy uwierzytelniania: podstawowe informacje | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Logowanie jednokrotne przy użyciu aplikacji sieci Web
Aplikacje sieci Web są popularne niezwykle. Aplikacje sieci Web są hostowane przez różne firmy i udostępniane jako usługa. Niektóre popularne przykłady usługi Web Apps obejmują Microsoft 365, GitHub i Salesforce, a istnieją tysiące innych. Użytkownicy uzyskują dostęp do aplikacji sieci Web za pomocą przeglądarki sieci Web na swoim komputerze. Logowanie jednokrotne umożliwia osobom Nawigowanie między różnymi aplikacjami sieci Web bez konieczności wielokrotnego logowania się.

Aby dowiedzieć się, jak działa Logowanie jednokrotne przy użyciu aplikacji sieci Web, zapoznaj się z tymi dwoma filmami wideo.

Podstawowe informacje dotyczące uwierzytelniania: aplikacje sieci Web | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Podstawowe informacje uwierzytelniania: Logowanie jednokrotne w sieci Web | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Aplikacje hostowane w chmurze i lokalnie
Sposób implementacji logowania jednokrotnego zależy od lokalizacji, w której aplikacja jest hostowana. Kwestie hostingu ze względu na sposób kierowania ruchu sieciowego w celu uzyskania dostępu do aplikacji. Jeśli aplikacja jest hostowana i dostępna za pośrednictwem sieci lokalnej o nazwie aplikacja lokalna, nie ma potrzeby uzyskiwania dostępu do Internetu w celu korzystania z aplikacji. Jeśli aplikacja jest hostowana w innym miejscu, nazywana aplikacją hostowaną w chmurze, użytkownicy będą musieli uzyskać dostęp do Internetu, aby móc korzystać z aplikacji.

> [!TIP]
> Aplikacje hostowane w chmurze są nazywane również aplikacjami typu oprogramowanie jako usługa (SaaS). 

Logowanie jednokrotne dla aplikacji hostowanych w chmurze jest proste. Poinformujemy dostawcę tożsamości o tym, że jest on używany w aplikacji. Następnie skonfigurujesz aplikację do zaufania dostawcy tożsamości. Aby dowiedzieć się, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji, zapoznaj się z [serią szybkiego startu w zarządzaniu aplikacjami](add-application-portal.md).

> [!TIP]
> Terminy w chmurze i Internecie są często używane zamiennie. Przyczyną tego problemu musi być diagramy sieciowe. Często należy zauważyć, że duże sieci komputerowe mają kształt chmury na diagramie, ponieważ nie jest to możliwe do rysowania każdy składnik. Internet to najbardziej dobrze znana sieć, dzięki czemu można łatwo korzystać z terminów zamiennie. Jednak każda sieć komputerowa może być wykorzystana z chmury.

Możesz również użyć logowania jednokrotnego dla aplikacji lokalnych. Technologia lokalnego logowania jednokrotnego jest nazywana serwerem proxy aplikacji. Aby dowiedzieć się więcej na ten temat, zobacz [Opcje logowania](sso-options.md)jednokrotnego.

## <a name="multiple-identity-providers"></a>Wielu dostawców tożsamości
Po skonfigurowaniu logowania jednokrotnego do pracy między wieloma dostawcami tożsamości jest on nazywany federacyjnym. Aby dowiedzieć się, jak działa Federacja Federacji, zapoznaj się z tym wideo.

Podstawowe informacje uwierzytelniania: Federacja | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Następne kroki
* [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
* [Opcje logowania jednokrotnego](sso-options.md)
