---
title: Dostawcy tożsamości dla tożsamości zewnętrznych — Azure AD
description: Dowiedz się, jak używać usługi Azure AD jako domyślnego dostawcy tożsamości na potrzeby udostępniania użytkownikom zewnętrznym.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ead05598c6ca4d096e1a68c8d640938ecd771c2
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355515"
---
# <a name="identity-providers-for-external-identities"></a>Dostawcy tożsamości dla tożsamości zewnętrznych

*Dostawca tożsamości* tworzy, i przechowuje informacje dotyczące tożsamości oraz zarządza nimi, zapewniając jednocześnie aplikacjom usługi uwierzytelniania. W przypadku udostępniania aplikacji i zasobów użytkownikom zewnętrznym usługa Azure AD jest domyślnym dostawcą tożsamości na potrzeby udostępniania. Oznacza to, że w przypadku zapraszania użytkowników zewnętrznych, którzy już mają usługę Azure AD lub konto Microsoft, mogą oni automatycznie logować się bez dalszej konfiguracji.

Można jednak umożliwić użytkownikom logowanie się przy użyciu różnych dostawców tożsamości.

- **Google**: w usłudze Google Federation użytkownicy zewnętrzni mogą wykorzystać zaproszenia od Ciebie, logując się do aplikacji przy użyciu własnych kont usługi Gmail. Usługi Google Federation można także używać w przepływach użytkownika samoobsługowego rejestrowania.
   > [!IMPORTANT]
   > **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).

   > [!NOTE]
   > W bieżącej wersji zapoznawczej samoobsługowej rejestracji, jeśli przepływ użytkownika jest skojarzony z aplikacją, a użytkownik wyśle użytkownikowi zaproszenie do tej aplikacji, użytkownik nie będzie mógł skorzystać z konta usługi Gmail w celu zrealizowania zaproszenia. W ramach tego problemu użytkownik może przejść przez proces tworzenia konta samoobsługowego. Mogą oni lub korzystać z zaproszenia, uzyskując dostęp do innej aplikacji lub portalu Moje aplikacje pod adresem https://myapps.microsoft.com .

- **Facebook**: podczas kompilowania aplikacji można skonfigurować samoobsługowe rejestrowanie i włączyć Federacji w serwisie Facebook, dzięki czemu użytkownicy mogą zarejestrować się w aplikacji przy użyciu własnych kont w serwisie Facebook. Usługi Facebook można używać tylko w przypadku przepływów użytkowników samoobsługowego rejestrowania i nie są dostępne jako opcja logowania, gdy użytkownicy korzystają z zaproszeń.

- **Federacja bezpośrednia**: można również skonfigurować bezpośrednią Federacji z dowolnym zewnętrznym dostawcą tożsamości, który obsługuje protokoły SAML lub WS-Fed. Bezpośrednia Federacja umożliwia użytkownikom zewnętrznym realizowanie zaproszeń od Ciebie, logując się do aplikacji przy użyciu istniejących kont społecznościowych lub firmowych. 
   > [!NOTE]
   > Bezpośrednich dostawców tożsamości Federacji nie można używać w przepływach użytkownika samoobsługowego rejestrowania.


## <a name="how-it-works"></a>Jak to działa

Funkcja samoobsługowego rejestrowania tożsamości zewnętrznych usługi Azure AD umożliwia użytkownikom rejestrowanie się przy użyciu konta usługi Azure AD, Google lub Facebook. Aby skonfigurować dostawców tożsamości społecznościowych w dzierżawie usługi Azure AD, należy utworzyć aplikację dla każdego dostawcy tożsamości i skonfigurować poświadczenia. Uzyskasz identyfikator klienta lub aplikacji oraz klucz tajny klienta lub aplikacji, który można następnie dodać do dzierżawy usługi Azure AD.

Po dodaniu dostawcy tożsamości do dzierżawy usługi Azure AD:

- W przypadku zapraszania użytkownika zewnętrznego do aplikacji lub zasobów w organizacji użytkownik zewnętrzny może zalogować się przy użyciu własnego konta z tym dostawcą tożsamości.
- Po włączeniu samoobsługowego [rejestrowania się](self-service-sign-up-overview.md) w aplikacjach użytkownicy zewnętrzni mogą zarejestrować się w aplikacjach przy użyciu własnych kont z dodanymi dostawcami tożsamości.

> [!NOTE]
> Usługa Azure AD jest domyślnie włączona w celu samoobsługowego rejestrowania się, więc użytkownicy zawsze mają możliwość rejestracji przy użyciu konta usługi Azure AD.

Podczas realizowania zaproszenia lub rejestracji w aplikacji użytkownik zewnętrzny ma możliwość logowania się i uwierzytelniania za pomocą dostawcy tożsamości społecznościowej:

![Zrzut ekranu przedstawiający ekran logowania z opcjami Google i Facebook](media/identity-providers/sign-in-with-social-identity.png)

Aby zapewnić optymalne środowisko logowania, sfederować się z dostawcami tożsamości wszędzie tam, gdzie to możliwe, aby zaproszony Goście mogli bezproblemowo korzystać z aplikacji.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dodać dostawców tożsamości do logowania się do aplikacji, zapoznaj się z następującymi artykułami:

- [Dodaj firmę Google](google-federation.md) do listy dostawców tożsamości społecznościowych
- [Dodawanie usługi Facebook](facebook-federation.md) do listy dostawców tożsamości społecznościowych
- [Skonfiguruj bezpośrednią Federacji](direct-federation.md) z każdą organizacją, której dostawca tożsamości obsługuje protokół SAML 2,0 lub WS-Fed. Należy zauważyć, że Federacja bezpośrednia nie jest opcją dla przepływów użytkownika samoobsługowego rejestrowania.
