---
title: Protokoły uwierzytelniania w Azure Active Directory B2C | Microsoft Docs
description: Jak kompilować aplikacje bezpośrednio przy użyciu protokołów obsługiwanych przez Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78183909"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: protokoły uwierzytelniania
Azure Active Directory B2C (Azure AD B2C) zapewnia tożsamość jako usługę dla aplikacji przez obsługę dwóch standardowych protokołów branżowych: OpenID Connect Connect i OAuth 2,0. Usługa jest zgodna ze standardami, ale wszystkie dwie implementacje tych protokołów mogą mieć delikatne różnice.

Informacje przedstawione w tym przewodniku są przydatne w przypadku pisania kodu przez bezpośrednie wysłanie i obsługę żądań HTTP, a nie za pomocą biblioteki Open Source. Zalecamy zapoznanie się z tą stroną przed szczegółowe szczegółowych informacji dotyczących poszczególnych protokołów. Ale jeśli znasz już Azure AD B2C, możesz przejść bezpośrednio do [przewodnika odwołania do protokołu](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Podstawy
Każda aplikacja, która używa Azure AD B2C musi być zarejestrowana w katalogu B2C w [Azure Portal](https://portal.azure.com). Proces rejestracji aplikacji polega na zgromadzeniu kilku wartości i przypisaniu ich do aplikacji:

* **Identyfikator aplikacji** który w sposób unikatowy identyfikuje aplikację.
* Identyfikator **URI przekierowania** lub **Identyfikator pakietu** , który może być używany do kierowania odpowiedzi z powrotem do aplikacji.
* Kilka innych wartości specyficznych dla scenariusza. Aby uzyskać więcej informacji, Dowiedz się, [jak zarejestrować aplikację](tutorial-register-applications.md).

Po zarejestrowaniu aplikacji komunikuje się ona z usługą Azure Active Directory (Azure AD), wysyłając żądania do punktu końcowego:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

W przypadku niemal wszystkich przepływów połączeń OAuth i OpenID Connect cztery strony są objęte wymianą:

![Diagram przedstawiający cztery role OAuth 2,0](./media/protocols-overview/protocols_roles.png)

* **Serwer autoryzacji** jest punktem końcowym usługi Azure AD. Bezpiecznie obsługuje wszystkie informacje dotyczące użytkowników i dostępu. Obsługuje także relacje zaufania między stronami w przepływie. Jest on odpowiedzialny za weryfikowanie tożsamości użytkownika, przydzielanie i odwoływanie dostępu do zasobów oraz wystawianie tokenów. Jest on również znany jako dostawca tożsamości.

* **Właścicielem zasobu** jest zazwyczaj użytkownik końcowy. Jest to strona, która jest właścicielem danych i ma możliwość zezwalania stronom trzecim na dostęp do tych danych lub zasobów.

* **Klient OAuth** to aplikacja. Jest on identyfikowany przez identyfikator aplikacji. Zwykle jest to strona, z którą użytkownicy końcowi pracują. Żąda również tokenów z serwera autoryzacji. Właściciel zasobu musi udzielić klientowi uprawnień dostępu do zasobu.

* **Serwer zasobów** jest miejscem, w którym znajdują się zasoby lub dane. Ufa serwerowi autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta protokołu OAuth. Używa również tokenów dostępu okaziciela, aby zapewnić, że dostęp do zasobu może być przyznany.

## <a name="policies-and-user-flows"></a>Zasady i przepływy użytkowników
Raczej, zasady Azure AD B2C to najważniejsze funkcje usługi. Azure AD B2C rozszerza standardowe protokoły uwierzytelniania OAuth 2,0 i OpenID Connect, wprowadzając zasady. Umożliwiają one Azure AD B2C wykonywanie znacznie więcej niż proste uwierzytelnianie i autoryzację.

Aby ułatwić skonfigurowanie najczęstszych zadań związanych z tożsamościami, Portal Azure AD B2C obejmuje wstępnie zdefiniowane, konfigurowalne zasady o nazwie **przepływy użytkownika**. Przepływy użytkownika w pełni opisują środowiska tożsamości konsumentów, w tym rejestrowanie, logowanie i edytowanie profilów. Przepływy użytkowników można definiować w administracyjnym interfejsie użytkownika. Mogą być wykonywane przy użyciu specjalnego parametru zapytania w żądaniach uwierzytelniania HTTP.

Zasady i przepływy użytkowników nie są standardowymi funkcjami protokołu OAuth 2,0 i OpenID Connect Connect, dlatego należy zająć się tym czasem. Aby uzyskać więcej informacji, zobacz [Podręcznik dotyczący Azure AD B2C przepływu użytkownika](user-flow-overview.md).

## <a name="tokens"></a>Tokeny
Azure AD B2C implementacja OAuth 2,0 i OpenID Connect Connect wykonuje duże użycie tokenów okaziciela, w tym tokenów okaziciela, które są reprezentowane jako tokeny sieci Web JSON (JWTs). Token okaziciela jest lekkim tokenem zabezpieczającym, który przyznaje "okaziciela" dostęp do chronionego zasobu.

Osoba przewożąca jest stroną, która może przedstawić token. Usługa Azure AD najpierw musi uwierzytelnić stronę, zanim będzie mogła odebrać token okaziciela. Ale jeśli nie zostaną podjęte wymagane kroki w celu zabezpieczenia tokenu podczas przesyłania i przechowywania, można je przechwycić i wykorzystać przez niezamierzoną stronę.

Niektóre tokeny zabezpieczające mają wbudowane mechanizmy uniemożliwiające korzystanie z nich przez osoby nieupoważnione, ale tokeny okaziciela nie mają tego mechanizmu. Muszą być transportowane za pośrednictwem bezpiecznego kanału, takiego jak Transport Layer Security (HTTPS).

Jeśli token okaziciela jest przesyłany poza bezpiecznym kanałem, złośliwa strona może korzystać z ataku typu man-in-the-Middle w celu uzyskania tokenu i używania go do uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie w przypadku, gdy tokeny okaziciela są przechowywane lub buforowane do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła tokeny okaziciela i przechowuje je w bezpieczny sposób.

Dodatkowe zagadnienia dotyczące zabezpieczeń tokenu nośnego znajdują się w [dokumencie RFC 6750 sekcja 5](https://tools.ietf.org/html/rfc6750).

Więcej informacji na temat różnych typów tokenów używanych w Azure AD B2C są dostępne w [dokumentacji tokenu usługi Azure AD](tokens-overview.md).

## <a name="protocols"></a>Protokoły
Gdy wszystko będzie gotowe do przejrzenia przykładowych żądań, możesz zacząć od jednego z następujących samouczków. Każdy odnosi się do określonego scenariusza uwierzytelniania. Jeśli potrzebujesz pomocy w ustaleniu, który z nich jest odpowiedni dla Ciebie, sprawdź [typy aplikacji, które można skompilować przy użyciu Azure AD B2C](application-types.md).

* [Tworzenie aplikacji mobilnych i natywnych przy użyciu protokołu OAuth 2,0](authorization-code-flow.md)
* [Tworzenie aplikacji sieci Web za pomocą OpenID Connect Connect](openid-connect.md)
* [Tworzenie aplikacji jednostronicowych przy użyciu niejawnego przepływu OAuth 2,0](implicit-flow-single-page-application.md)

