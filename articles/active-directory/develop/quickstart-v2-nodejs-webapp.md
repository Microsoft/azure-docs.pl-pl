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
ms.openlocfilehash: 643305057490cc550a5a8e39a892297b000cbc8e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169413"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Szybki Start: Dodawanie logowania za pomocą usługi OpenID Connect nawiązywanie połączenia z aplikacją sieci Web Node.js

W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który pokazuje, jak skonfigurować uwierzytelnianie OpenID Connect Connect w aplikacji sieci Web skompilowanej przy użyciu Node.js w języku Express. Przykład jest przeznaczony do uruchamiania na dowolnej platformie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Rejestrowanie aplikacji
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. Jeśli Twoje konto jest obecne w więcej niż jednej dzierżawie usługi Azure AD:
    - Wybierz swój profil z menu w prawym górnym rogu strony, a następnie **Przełącz katalog**.
    - Zmień sesję na dzierżawę usługi Azure AD, w której chcesz utworzyć aplikację.

1. Przejdź do [Azure Active Directory > rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) , aby zarejestrować aplikację.

1. Wybierz pozycję **Nowa rejestracja.**

1. Gdy zostanie wyświetlona strona **zarejestruj aplikację** , wprowadź informacje rejestracyjne swojej aplikacji:
    - W sekcji **Nazwa** wprowadź zrozumiałą nazwę, która będzie wyświetlana użytkownikom aplikacji. Na przykład: MyWebApp
    - W sekcji **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym i osobiste konta Microsoft (np. Skype, Xbox, Outlook.com)**.

    Jeśli istnieje więcej niż jeden identyfikator URI przekierowania, należy dodać je z karty **uwierzytelnianie** później po pomyślnym utworzeniu aplikacji.

1. Wybierz pozycję **zarejestruj** , aby utworzyć aplikację.

1. Na stronie **Przegląd** aplikacji Znajdź wartość **Identyfikator aplikacji (klienta)** i Zapisz ją jako nowszą. Ta wartość będzie potrzebna do późniejszego skonfigurowania aplikacji w tym projekcie.

1. Na liście stron dla aplikacji wybierz pozycję **Uwierzytelnianie**.
    - W sekcji **adresy URI przekierowania** wybierz pozycję **Sieć Web** w polu kombi i wprowadź następujący identyfikator URI przekierowania: `http://localhost:3000/auth/openid/return`
    - W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:3000`.
    - W sekcji **Ustawienia zaawansowane > niejawnego** przypisywania Sprawdź **tokeny identyfikatorów** , ponieważ ten przykład wymaga włączenia [niejawnego przepływu dotacji](./v2-oauth2-implicit-grant-flow.md) do logowania użytkownika.

1. Wybierz pozycję **Zapisz**.

1. Na stronie **certyfikaty & wpisy tajne** w sekcji **klucze tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**.
    - Wprowadź opis klucza (dla wpisu tajnego aplikacji wystąpienia).
    - Wybierz kluczowy okres trwania z przedziału **1 roku, w ciągu 2 lat** lub **nigdy nie wygasa**.
    - Po kliknięciu przycisku **Dodaj** zostanie wyświetlona wartość klucza. Skopiuj wartość klucza i Zapisz ją w bezpiecznej lokalizacji.

    Ten klucz będzie potrzebny później do skonfigurowania aplikacji. Ta wartość klucza nie będzie ponownie wyświetlana ani nie można jej pobrać z innych metod, dlatego Zapisz ją tak szybko, jak to będzie widoczne w Azure Portal.

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