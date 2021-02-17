---
title: 'Samouczek: udzielanie dostępu do Node.js internetowego interfejsu API z aplikacji klasycznej'
description: Samouczek dotyczący sposobu używania Active Directory B2C do ochrony internetowego interfejsu API Node.js i wywoływania go z poziomu aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555613"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Samouczek — udzielanie dostępu do internetowego interfejsu API platformy Node.js z aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

W tym samouczku przedstawiono sposób wywoływania Node.js internetowego interfejsu API chronionego przez program Azure Active Directory B2C (Azure AD B2C) z aplikacji klasycznej programu Windows Presentation Foundation (WPF), również chronionej przez Azure AD B2C.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i wymagania wstępne w [samouczku: uwierzytelnianie użytkowników w natywnym kliencie klasycznym](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zapisz wartość w obszarze **zakresy** dla `demo.read` zakresu, który ma być używany w późniejszym kroku podczas konfigurowania aplikacji klasycznej. Pełny zakres wartości jest podobny do `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z natywnej aplikacji klienckiej, należy przyznać zarejestrowanej natywnej aplikacji klienckiej uprawnienia do internetowego interfejsu API, który został zarejestrowany w Azure AD B2C.

W samouczku wymagań wstępnych zarejestrowano natywną aplikację kliencką o nazwie *nativeapp1*. Poniższe kroki umożliwiają skonfigurowanie natywnej rejestracji aplikacji przy użyciu zakresów interfejsu API, które zostały uwidocznione dla *webapi1* w poprzedniej sekcji. Dzięki temu aplikacja klasyczna może uzyskać token dostępu z Azure AD B2C, z którego korzysta interfejs API sieci Web w celu sprawdzenia i zapewnienia dostępu do zakresu zasobów. W dalszej części tego samouczka konfigurujesz i uruchamiasz zarówno przykład kodu aplikacji klasycznych, jak i interfejsu API sieci Web.

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz natywną aplikację kliencką, która powinna mieć dostęp do interfejsu API. Na przykład *nativeapp1*.
1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia** wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** .
1. Wybierz interfejs API, do którego ma zostać udzielony dostęp do natywnej aplikacji klienckiej. Na przykład *webapi1*.
1. W obszarze **uprawnienie** rozwiń pozycję **Demonstracja**, a następnie wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)**.
1. Wybierz obecnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan** dla obu zakresów. Propagowanie uprawnień może potrwać kilka minut.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz pozycję *nativeapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz przycisk **OK**.

* * *

Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z klasycznej aplikacji WPF. Aplikacja klasyczna uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-samples"></a>Skonfiguruj przykłady

Teraz, gdy internetowy interfejs API jest zarejestrowany i masz dostępne zakresy i uprawnienia, skonfigurujesz przykłady aplikacji klasycznych i interfejsów API sieci Web do korzystania z dzierżawy Azure AD B2C.

### <a name="update-the-desktop-application"></a>Aktualizowanie aplikacji klasycznej

W ramach wymagań wstępnych dotyczących tego artykułu zmodyfikowano [aplikację klasyczną WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) , aby umożliwić logowanie się przy użyciu przepływu użytkowników w dzierżawie Azure AD B2C. W tej sekcji zostanie zaktualizowana ta sama aplikacja, aby odwołać się do internetowego interfejsu API, który został zarejestrowany wcześniej, *webapi1*.

1. Otwórz rozwiązanie **Active-Directory-B2C-WPF** ( `active-directory-b2c-wpf.sln` ) w programie Visual Studio.
1. W projekcie **Active-Directory-B2C-WPF** otwórz plik *App.XAML.cs* i Znajdź następujące definicje zmiennych.
    1. Zastąp wartość zmiennej wartością `ApiScopes` zanotowaną wcześniej podczas definiowania **demonstracji. zakres odczytu** .
    1. Zastąp wartość `ApiEndpoint` zmiennej **adresem URI przekierowania** , który został zarejestrowany wcześniej podczas rejestrowania internetowego interfejsu API (na przykład *webapi1*) w dzierżawie.

    Oto przykład:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Pobierz i zaktualizuj przykład interfejsu API Node.js

Następnie Pobierz przykładowy kod internetowego interfejsu API Node.js z usługi GitHub i skonfiguruj go tak, aby korzystał z internetowego interfejsu API zarejestrowanego w dzierżawie Azure AD B2C.

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Przykładowy internetowy interfejs API platformy Node.js korzysta z biblioteki Passport.js, aby umożliwić usłudze Azure AD B2C ochronę wywołań do interfejsu API.

1. Otwórz plik `index.js`.
1. Zaktualizuj te definicje zmiennych przy użyciu następujących wartości. Przejdź `<web-API-application-ID>` do **identyfikatora aplikacji (klienta)** , który został zarejestrowany wcześniej (*webapi1*). Zmień `<your-b2c-tenant>` nazwę dzierżawy Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Ponieważ korzystasz z interfejsu API lokalnie, zaktualizuj ścieżkę w marszrucie dla metody GET `/` zamiast lokalizacji aplikacji demonstracyjnej `/hello` :

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Uruchamianie przykładów

### <a name="run-the-nodejs-web-api"></a>Uruchamianie interfejsu API sieci Web Node.js

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Uruchom następujące polecenia:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Uruchamianie aplikacji klasycznej

1. Otwórz rozwiązanie **active-directory-b2c-wpf** w programie Visual Studio.
2. Naciśnij klawisz **F5**, aby uruchomić aplikację klasyczną.
3. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji klasycznej](tutorial-desktop-app.md).
4. Wybierz przycisk **wywoływania interfejsu API** .

Aplikacja klasyczna wysyła żądanie do uruchomionego lokalnie interfejsu API sieci Web i po sprawdzeniu prawidłowego tokenu dostępu wyświetla nazwę wyświetlaną zalogowanego użytkownika.

![Wyświetlana nazwa wyświetlana w górnym okienku aplikacji klasycznej WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Aplikacja klasyczna chroniona przez Azure AD B2C wywołuje lokalnie uruchomiony interfejs API sieci Web, który jest również chroniony przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Aktualizacja przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Dodawanie dostawców tożsamości do aplikacji w Azure Active Directory B2C](add-identity-provider.md)