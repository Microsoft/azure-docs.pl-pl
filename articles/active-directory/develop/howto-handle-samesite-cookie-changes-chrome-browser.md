---
title: Jak radzić sobie ze zmianami plików cookie SameSite w przeglądarce Chrome | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak radzić sobie ze zmianami plików cookie sameSite w przeglądarce Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: f28d3722d56582bd925d31b43b4a0219bca2ae30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534605"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Obsługa zmian plików cookie SameSite w przeglądarce Chrome

## <a name="what-is-samesite"></a>Co to jest SameSite?

`SameSite`jest właściwością, którą można ustawić w plikach cookie HTTP, aby zapobiec atakom między site request forgery (CSRF) w aplikacjach internetowych:

- Gdy `SameSite` jest ustawiony na **Lax,** plik cookie jest wysyłany w żądaniach w tej samej witrynie i w żądaniach GET z innych witryn. Nie jest wysyłany w żądaniach GET, które są międzydomenowe.
- Wartość **Strict** zapewnia, że plik cookie jest wysyłany w żądaniach tylko w tej samej witrynie.

Domyślnie `SameSite` wartość NIE jest ustawiana w przeglądarkach i dlatego nie ma żadnych ograniczeń dotyczących plików cookie wysyłanych w żądaniach. Aplikacja musiałaby wyrazić zgodę na ochronę CSRF, ustawiając **lax** lub **strict** według ich wymagań.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite zmiany i wpływ na uwierzytelnianie

Ostatnie [aktualizacje standardów na SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) zaproponować ochronę `SameSite` aplikacji poprzez domyślne zachowanie, gdy żadna wartość nie jest ustawiona na Lax. To ograniczenie oznacza, że pliki cookie będą ograniczone w żądaniach HTTP, z wyjątkiem get wykonanych z innych witryn. Ponadto wprowadzono wartość **None,** aby usunąć ograniczenia dotyczące wysyłanych plików cookie. Te aktualizacje zostaną wkrótce wydane w nadchodzącej wersji przeglądarki Chrome.

Gdy aplikacje sieci web uwierzytelniają się za pomocą platformy Microsoft Identity przy użyciu trybu odpowiedzi "form_post", serwer logowania odpowiada aplikacji przy użyciu protokołu HTTP POST do wysyłania tokenów lub kodu uwierzytelniania. Ponieważ to żądanie jest żądaniem `login.microsoftonline.com` międzydomenowym (z domeny — na przykład `https://contoso.com/auth`) plikami cookie ustawionymi przez aplikację, które są teraz objęte nowymi regułami w Chrome. Pliki cookie, które muszą być używane w scenariuszach między witrynami, to pliki cookie, które przechowują wartości *stanu* i *wartości jednorazowe,* które są również wysyłane w żądaniu logowania. Istnieją inne pliki cookie porzucone przez usługę Azure AD do przechowywania sesji.

Jeśli nie zaktualizujesz aplikacji sieci web, to nowe zachowanie spowoduje błędy uwierzytelniania.

## <a name="mitigation-and-samples"></a>Łagodzenie i próbki

Aby przezwyciężyć błędy uwierzytelniania, aplikacje sieci web uwierzytelniające się za pomocą platformy tożsamości firmy Microsoft mogą ustawić `SameSite` właściwość `None` na pliki cookie, które są używane w scenariuszach międzydomenowych podczas uruchamiania w przeglądarce Chrome.
Inne przeglądarki (zobacz [tutaj,](https://www.chromium.org/updates/same-site/incompatible-clients) aby uzyskać pełną `SameSite` listę) postępuj zgodnie `SameSite=None` z poprzednim zachowaniem i nie będą zawierać plików cookie, jeśli jest ustawiona.
Dlatego, aby obsługiwać uwierzytelnianie w wielu przeglądarkach aplikacje internetowe będą musiały ustawić `SameSite` wartość `None` tylko w Chrome i pozostawić wartość pustą w innych przeglądarkach.

Takie podejście zostało zademonstrowane w naszych przykładach kodu poniżej.

# <a name="net"></a>[.NET](#tab/dotnet)

W poniższej tabeli przedstawiono żądania ściągnięcia, które działały wokół zmian w tym samymwityłm w naszych ASP.NET i ASP.NET próbkach core.

| Przykład | Żądanie ściągnięcia |
| ------ | ------------ |
|  [ASP.NET core aplikacji sieci web przyrostowe samouczek](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Ten sam plik cookie witryny naprawić #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [przykład ASP.NET aplikacji sieci Web MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Ten sam plik cookie witryny naprawić #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Ten sam plik cookie witryny naprawić #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

szczegółowe informacje na temat obsługi plików cookie SameSite w ASP.NET i ASP.NET Core można znaleźć również w:

- [Pracuj z plikami cookie SameSite w ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET Blog na ten problem miejscu](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Przykład |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Przykład | Żądanie ściągnięcia |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Ten sam plik cookie witryny naprawić #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Ten sam plik cookie witryny naprawić #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym samymwitynie i scenariuszu aplikacji sieci Web:

> [!div class="nextstepaction"]
> [Google Chrome często zadawane pytania na SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Chromium SameSite strona](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje się do użytkowników](scenario-web-app-sign-user-overview.md)