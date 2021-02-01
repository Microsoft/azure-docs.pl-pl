---
title: 'Szybki Start: Dodawanie logowania użytkownika do aplikacji internetowej Node.js | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak zaimplementować uwierzytelnianie w aplikacji sieci Web Node.js za pomocą OpenID Connect Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: fec130ff02d9796fb9a6cea7a831bbdcb25cb1ed
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225634"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Szybki Start: Dodawanie logowania za pomocą usługi OpenID Connect nawiązywanie połączenia z aplikacją sieci Web Node.js

W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który pokazuje, jak skonfigurować uwierzytelnianie OpenID Connect Connect w aplikacji sieci Web skompilowanej przy użyciu Node.js w języku Express. Przykład jest przeznaczony do uruchamiania na dowolnej platformie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Rejestrowanie aplikacji

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji, na przykład `MyWebApp` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
1. W sekcji **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)**.

    Jeśli istnieje więcej niż jeden identyfikator URI przekierowania, Dodaj je z karty **uwierzytelnianie** później po pomyślnym utworzeniu aplikacji.

1. Wybierz pozycję **zarejestruj** , aby utworzyć aplikację.
1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Ta wartość będzie potrzebna do późniejszego skonfigurowania aplikacji w tym projekcie.
1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. Wybierz pozycję **Dodaj platformę**  >  **sieci Web**. 
1. W sekcji **identyfikatory URI przekierowania** wprowadź wartość `http://localhost:3000/auth/openid/return` .
1. Wprowadź **adres URL wylogowania z przodu** `https://localhost:3000` .
1. W sekcji **niejawne uprawnienia do przypisywania i hybrydowych przepływów** wybierz opcję **identyfikatory tokenów** , ponieważ ten przykład wymaga, aby [przepływ niejawnego przydzielenia](./v2-oauth2-implicit-grant-flow.md) był włączony do logowania użytkownika.
1. Wybierz pozycję **Konfiguruj**.
1. W obszarze **Zarządzaj** wybierz pozycję **Certyfikaty &** wpisy tajne  >  **nowy klucz tajny klienta**.
1. Wprowadź opis klucza (dla wpisu tajnego aplikacji wystąpienia).
1. Wybierz kluczowy okres trwania z przedziału **1 roku, w ciągu 2 lat** lub **nigdy nie wygasa**.
1. Wybierz pozycję **Dodaj**. Zostanie wyświetlona wartość klucza. Skopiuj wartość klucza i Zapisz ją w bezpiecznym miejscu do późniejszego użycia.


## <a name="download-the-sample-application-and-modules"></a>Pobieranie przykładowej aplikacji i modułów

Następnie Sklonuj przykładowe repozytorium i zainstaluj moduły NPM.

Z powłoki lub wiersza polecenia:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

lub

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

W katalogu głównym projektu uruchom polecenie:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurowanie aplikacji

Podaj parametry w config.js zgodnie z `exports.creds` instrukcją.

* Zaktualizuj `<tenant_name>` `exports.identityMetadata` program przy użyciu nazwy dzierżawy usługi Azure AD w formacie \* . onmicrosoft.com.
* Zaktualizuj `exports.clientID` przy użyciu identyfikatora aplikacji zanotowanego w obszarze Rejestracja aplikacji.
* Zaktualizuj `exports.clientSecret` klucz tajny aplikacji zapisany w obszarze Rejestracja aplikacji.
* Zaktualizuj `exports.redirectUrl` przy użyciu identyfikatora URI przekierowania zanotowanego w obszarze Rejestracja aplikacji.

**Opcjonalna konfiguracja dla aplikacji produkcyjnych:**

* Zaktualizuj `exports.destroySessionUrl` w config.js, jeśli chcesz użyć innego `post_logout_redirect_uri` .

* Ustaw `exports.useMongoDBSessionStore` w config.js na wartość true, jeśli chcesz używać [MongoDB](https://www.mongodb.com) lub innych [zgodnych magazynów sesji](https://github.com/expressjs/session#compatible-session-stores).
Domyślnym magazynem sesji w tym przykładzie jest `express-session` . Domyślny magazyn sesji nie jest odpowiedni dla środowiska produkcyjnego.

* Zaktualizuj `exports.databaseUri` , jeśli chcesz użyć magazynu sesji MongoDB i innego identyfikatora URI bazy danych.

* Aktualizacja `exports.mongoDBSessionMaxAge` . W tym miejscu możesz określić, jak długo ma być przechowywane sesja w mongoDB. Jednostka jest sekunda.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Uruchom usługę mongoDB. Jeśli używasz magazynu sesji mongoDB w tej aplikacji, musisz [zainstalować MongoDB](http://www.mongodb.org/) i najpierw uruchomić usługę. Jeśli używasz domyślnego magazynu sesji, możesz pominąć ten krok.

Uruchom aplikację przy użyciu następującego polecenia z poziomu wiersza polecenia.

```
$ node app.js
```

**Czy dane wyjściowe serwera są trudne do zrozumienia?:** Używamy `bunyan` do rejestrowania w tym przykładzie. Konsola nie ma sensu, o ile nie zostanie również zainstalowana Bunyan i uruchomiony serwer, jak powyżej, ale potok go przez plik binarny Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Wszystko gotowe!

Serwer programu będzie działał pomyślnie `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat scenariusza aplikacji sieci Web obsługiwanego przez platformę Microsoft Identity:
> [!div class="nextstepaction"]
> [Aplikacja internetowa, która loguje się do użytkowników](scenario-web-app-sign-user-overview.md)