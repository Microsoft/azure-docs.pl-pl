---
title: Co to jest logowanie jednokrotne platformy Azure?
description: Dowiedz się, jak logowanie jednokrotne współpracuje z Azure Active Directory. Użyj logowania jednokrotnego, aby użytkownicy nie musieli pamiętać haseł dla każdej aplikacji. Użyj również logowania jednokrotnego, aby uprościć administrowanie zarządzaniem kontami.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 65e4b32c7c41eea6a885a09dd7ad772980595a9e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379506"
---
# <a name="what-is-single-sign-on-sso"></a>Co to jest logowanie jednokrotne?

Logowanie pojedyncze oznacza, że użytkownik nie musi logować się do każdej aplikacji, z których korzysta. Użytkownik loguje się raz i to poświadczenie jest również używane w innych aplikacjach.

Jeśli jesteś użytkownikiem końcowym, prawdopodobnie nie zależy Ci na szczegółach logowania jednokrotnego. Chcesz po prostu korzystać z aplikacji, które sprawią, że będziesz wydajnie pracować bez konieczności wpisywania hasła. Aplikacje można znaleźć pod adresami: https://myapps.microsoft.com .
 
Jeśli jesteś administratorem lub specjalistą IT, przeczytaj dalej, aby dowiedzieć się więcej na temat logowania jednokrotnego i sposobu jego wdrożenia na platformie Azure.

## <a name="single-sign-on-basics"></a>Podstawy logowania pojedynczego
Logowanie pojedynczego użytkownika to duży krok naprzód w sposobu logowania się użytkowników i korzystania z aplikacji. Systemy uwierzytelniania oparte na logiu pojedynczym są często nazywane "nowoczesnym uwierzytelnianiem". Nowoczesne uwierzytelnianie i logowanie pojedynczy należą do kategorii obliczeń o nazwie Zarządzanie tożsamościami i dostępem (IAM). Aby dowiedzieć się, co sprawia, że logowanie pojedynczej jest możliwe, zapoznaj się z tym filmem wideo.

Podstawy uwierzytelniania: Podstawowe | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Logowanie pojedyncze z aplikacjami internetowymi
Aplikacje internetowe są niezwykle popularne. Aplikacje internetowe są hostowane przez różne firmy i udostępniane jako usługa. Niektóre popularne przykłady aplikacji internetowych to Microsoft 365, GitHub i Salesforce, a istnieje wiele innych. Osoby mogą uzyskać dostęp do aplikacji internetowych za pomocą przeglądarki internetowej na swoim komputerze. Logowanie pojedyncze umożliwia użytkownikom przechodzenie między różnymi aplikacjami internetowymi bez konieczności wielokrotnego logowania.

Aby dowiedzieć się, jak logowanie pojedynczej działa z aplikacjami internetowymi, zapoznaj się z tymi dwoma filmami wideo.

Podstawy uwierzytelniania: Aplikacje internetowe | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Podstawy uwierzytelniania: Zasady logowania | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Aplikacje hostowane w chmurze a aplikacje hostowane lokalnie
Sposób implementacji logowania pojedynczego zależy od tego, gdzie jest hostowana aplikacja. Hosting ma znaczenie ze względu na sposób, w jaki ruch sieciowy jest przekierowyny w celu uzyskania dostępu do aplikacji. Jeśli aplikacja jest hostowana i uzyskiwana do niej dostęp za pośrednictwem sieci lokalnej nazywanej aplikacją lokalną, użytkownicy nie muszą uzyskiwać dostępu do Internetu, aby korzystać z aplikacji. Jeśli aplikacja jest hostowana w innym miejscu , nazywanym aplikacją hostowaną w chmurze, użytkownicy będą musieli uzyskać dostęp do Internetu, aby móc korzystać z aplikacji.

> [!TIP]
> Aplikacje hostowane w chmurze są również nazywane aplikacjami typu oprogramowanie jako usługa (SaaS). 

Logowanie pojedyncze dla aplikacji hostowanych w chmurze jest proste. Należy pouścić dostawcę tożsamości, że jest on używany dla aplikacji. Następnie skonfigurujesz aplikację tak, aby ufała dostawcy tożsamości. Aby dowiedzieć się, jak używać usługi Azure AD jako dostawcy tożsamości dla aplikacji, zobacz serię Szybki start [na temat zarządzania aplikacją.](add-application-portal.md)

> [!TIP]
> Terminy chmura i Internet są często używane zamiennie. Przyczyną tego jest to, że ma to związku z diagramami sieci. Często na diagramie są oznaczane duże sieci komputerowe z kształtem chmury, ponieważ nie można narysować każdego składnika. Internet jest najpowszechniej znaną siecią i dlatego łatwo jest używać terminów zamiennie. Jednak każdą sieć komputerową można wymyślić w chmurze.

Możesz również użyć logowania pojedynczego dla aplikacji lokalnych. Technologia do lokalnego logowania jednokrotnego jest nazywana serwer proxy aplikacji. Aby dowiedzieć się więcej na ten temat, zobacz [Opcje logowania pojedynczego](sso-options.md).

## <a name="multiple-identity-providers"></a>Wielu dostawców tożsamości
Po skonfigurowaniu logowania pojedynczego do pracy między wieloma dostawcami tożsamości jest ono nazywane federacją. Aby dowiedzieć się, jak działa federacja, zapoznaj się z tym wideo.

Podstawy uwierzytelniania: Federacja | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Następne kroki
* [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
* [Opcje logowania jednokrotnego](sso-options.md)
