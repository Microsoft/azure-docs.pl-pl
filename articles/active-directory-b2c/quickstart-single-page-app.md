---
title: 'Szybki Start: Konfigurowanie logowania dla aplikacji jednostronicowej (SPA)'
titleSuffix: Azure AD B2C
description: W tym przewodniku Szybki Start Uruchom przykładową aplikację jednostronicową, która używa Azure Active Directory B2C w celu zapewnienia logowania do konta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: db18c71b09cd9beb58799ad4fbcbc944061153fb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87921762"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Szybki start: konfigurowanie logowania dla aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) umożliwia zarządzanie tożsamościami w chmurze w celu zapewnienia ochrony aplikacji, firm i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami. W tym przewodniku Szybki start aplikacja jednostronicowa jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Konto społecznościowe w serwisie Facebook, Google lub Microsoft
- Przykład kodu z witryny GitHub: [Active-Directory-B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Możesz [pobrać archiwum zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) lub sklonować repozytorium:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Uruchom serwer za pomocą następującego polecenia w wierszu polecenia środowiska Node.js:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    Serwer uruchomiony przez *server.js* wyświetla port, na którym nasłuchuje:

    ```console
    Listening on port 6420...
    ```

1. Przejdź do adresu URL aplikacji. Na przykład `http://localhost:6420`.

    ![Przykładowa aplikacja jednostronicowa wyświetlana w przeglądarce](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Logowanie się przy użyciu swojego konta

1. Wybierz pozycję **Zaloguj** , aby rozpocząć podróż użytkownika.
1. Azure AD B2C przedstawia stronę logowania fikcyjnej firmy o nazwie Fabrikam dla przykładowej aplikacji sieci Web. Aby zarejestrować się przy użyciu dostawcy tożsamości społecznościowej, wybierz przycisk dostawcy tożsamości, którego chcesz użyć.

    ![Strona logowania lub rejestracji przedstawiająca przyciski dostawcy tożsamości](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Użytkownik uwierzytelnia się (loguje się) przy użyciu poświadczeń konta społecznościowego i autoryzuje aplikację do odczytywania informacji z konta społecznościowego. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto.

1. Zakończ proces logowania dla dostawcy tożsamości.

## <a name="access-a-protected-api-resource"></a>Uzyskiwanie dostępu do chronionego zasobu interfejsu API

Wybierz pozycję **interfejs API wywołania** , aby nazwa wyświetlana została zwrócona przez internetowy interfejs API jako obiekt JSON.

![Przykładowa aplikacja w przeglądarce pokazująca odpowiedź interfejsu API sieci Web](./media/quickstart-single-page-app/call-api-spa.png)

Przykładowa aplikacja jednostronicowa dołącza token dostępu do żądania wysłanego do chronionego zasobu internetowego interfejsu API.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne przewodniki Szybki start lub samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto przykładowej aplikacji jednostronicowej do:

- Zaloguj się przy użyciu dostawcy tożsamości społecznościowej
- Utwórz konto użytkownika Azure AD B2C (tworzone automatycznie podczas logowania)
- Wywoływanie internetowego interfejsu API chronionego przez Azure AD B2C

Wprowadzenie do tworzenia własnej dzierżawy usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)
