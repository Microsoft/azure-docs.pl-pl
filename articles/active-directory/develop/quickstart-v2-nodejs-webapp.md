---
title: Dodawanie logowania OIDC do aplikacji Node.js Web — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zaimplementować uwierzytelnianie w aplikacji sieci Web Node.js przy użyciu funkcji OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 1ff92b8a9477800477ebb2d79145ddaa78831f30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536067"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Szybki start: dodawanie logowania przy użyciu funkcji OpenID Connect do aplikacji sieci Web Node.js

W tym przewodniku Szybki start dowiesz się, jak skonfigurować uwierzytelnianie OpenID Connect w aplikacji sieci web utworzonej przy użyciu pliku Node.js w aplikacji Express. Próbka jest przeznaczona do uruchamiania na dowolnej platformie.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten przykład, należy:

* Instalowanie pliku Node.js zhttp://nodejs.org/

* [Konto Microsoft](https://www.outlook.com) lub [program deweloperski usługi Office 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Rejestrowanie aplikacji
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta służbowego lub konta firmy Microsoft.
1. Jeśli Twoje konto znajduje się w więcej niż jednej dzierżawie usługi Azure AD:
    - Wybierz swój profil z menu w prawym górnym rogu strony, a następnie **przełącz katalog**.
    - Zmień sesję na dzierżawę usługi Azure AD, w której chcesz utworzyć aplikację.

1. Przejdź do [usługi Azure Active Directory > rejestracji aplikacji,](https://go.microsoft.com/fwlink/?linkid=2083908) aby zarejestrować aplikację.

1. Wybierz **pozycję Nowa rejestracja.**

1. Po **wyświetleniu** strony Zarejestruj aplikację wprowadź informacje rejestracyjne aplikacji:
    - W sekcji **Nazwa** wprowadź opisową nazwę, która będzie wyświetlana użytkownikom aplikacji. Na przykład: MyWebApp
    - W sekcji **Obsługiwane typy kont** wybierz **pozycję Konta w dowolnym katalogu organizacyjnym i osobistych kontach Microsoft (np Outlook.com.**

    Jeśli istnieje więcej niż jeden przekierowanie identyfikatorów URI, należy dodać je z karty **Uwierzytelnianie** później po pomyślnym utworzeniu aplikacji.

1. Wybierz **zarejestruj się,** aby utworzyć aplikację.

1. Na stronie **Przegląd** aplikacji znajdź wartość **identyfikatora aplikacji (klienta)** i zarejestruj ją na później. Ta wartość będzie potrzebna do skonfigurowania aplikacji w dalszej części tego projektu.

1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
    - W sekcji **Przekierowanie identyfikatorów URI** wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujący identyfikator URI przekierowania:`http://localhost:3000/auth/openid/return`
    - W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `http://localhost:3000`.
    - W **ustawieniach zaawansowanych > niejawnych dotacji** sekcji, sprawdź **tokeny identyfikatora,** ponieważ w tym przykładzie wymaga [niejawnego przepływu dotacji,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) aby włączyć logowanie użytkownika.

1. Wybierz pozycję **Zapisz**.

1. Na stronie **Certyfikaty & wpisy tajne** w sekcji **Wpisy tajne klienta** wybierz pozycję Nowy klucz tajny **klienta**.
    - Wprowadź opis klucza (na przykład klucz tajny aplikacji).
    - Wybierz kluczowy czas trwania **w ciągu 1 roku, w ciągu 2 lat** lub nigdy nie **wygasa**.
    - Po kliknięciu przycisku **Dodaj** zostanie wyświetlona wartość klucza. Skopiuj wartość klucza i zapisz ją w bezpiecznym miejscu.

    Ten klucz będzie potrzebny później, aby skonfigurować aplikację. Ta wartość klucza nie będą wyświetlane ponownie, ani można pobrać za pomocą innych środków, więc należy go zarejestrować tak szybko, jak to jest widoczne z witryny Azure portal.

## <a name="download-the-sample-application-and-modules"></a>Pobierz przykładową aplikację i moduły

Następnie sklonuj przykładowe repozytorium i zainstaluj moduły NPM.

Z powłoki lub wiersza polecenia:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

lub

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Z katalogu głównego projektu uruchom polecenie:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurowanie aplikacji

Podaj `exports.creds` parametry w config.js zgodnie z instrukcją.

* Zaktualizuj `<tenant_name>` `exports.identityMetadata` za pomocą nazwy \*dzierżawy usługi Azure AD w formacie .onmicrosoft.com.
* Aktualizacja `exports.clientID` o identyfikator aplikacji zauważyć z rejestracji aplikacji.
* Aktualizacja `exports.clientSecret` z kluczem tajnym aplikacji zauważyć z rejestracji aplikacji.
* Aktualizacja `exports.redirectUrl` za pomocą identyfikatora URI przekierowania zauważyć z rejestracji aplikacji.

**Opcjonalna konfiguracja aplikacji produkcyjnych:**

* Zaktualizuj `exports.destroySessionUrl` w config.js, `post_logout_redirect_uri`jeśli chcesz użyć innego .

* Ustaw `exports.useMongoDBSessionStore` w config.js do true, jeśli chcesz użyć [mongoDB](https://www.mongodb.com) lub innych [kompatybilnych sklepów sesji](https://github.com/expressjs/session#compatible-session-stores).
Domyślnym magazynem sesji `express-session`w tym przykładzie jest . Domyślny magazyn sesji nie nadaje się do produkcji.

* Aktualizuj `exports.databaseUri`, jeśli chcesz użyć magazynu sesji mongodb i innego identyfikatora URI bazy danych.

* Aktualizacja `exports.mongoDBSessionMaxAge`. W tym miejscu można określić, jak długo chcesz zachować sesję w mongoDB. Urządzenie jest drugie(-e).

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Uruchom usługę mongoDB. Jeśli używasz magazynu sesji mongodb w tej aplikacji, musisz [zainstalować mongoDB](http://www.mongodb.org/) i najpierw uruchomić usługę. Jeśli używasz domyślnego magazynu sesji, możesz pominąć ten krok.

Uruchom aplikację przy użyciu następującego polecenia z wiersza polecenia.

```
$ node app.js
```

**Czy dane wyjściowe serwera są trudne do zrozumienia?:** Używamy `bunyan` do logowania w tym przykładzie. Konsola nie będzie mieć większego sensu dla Ciebie, chyba że również zainstalować bunyan i uruchomić serwer jak powyżej, ale rury to przez bunyan binarny:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Gotowe!

Serwer będzie działał pomyślnie `http://localhost:3000`na programie .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o scenariuszu aplikacji sieci Web, który obsługuje platforma tożsamości firmy Microsoft:
> [!div class="nextstepaction"]
> [Aplikacja sieci Web, która loguje się w scenariuszu użytkowników](scenario-web-app-sign-user-overview.md)
