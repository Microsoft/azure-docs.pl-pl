---
title: 'Szybki Start: Konfigurowanie logowania dla aplikacji sieci Web ASP.NET'
titleSuffix: Azure AD B2C
description: W tym przewodniku Szybki Start Uruchom przykładową aplikację sieci Web ASP.NET, która używa Azure Active Directory B2C w celu zapewnienia logowania do konta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 379ea9a0c2a49e92fa1c4882ea6e783b3b91e32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88163921"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Szybki Start: Konfigurowanie logowania dla aplikacji ASP.NET przy użyciu Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) umożliwia zarządzanie tożsamościami w chmurze w celu zapewnienia ochrony aplikacji, firm i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami. W tym przewodniku Szybki start aplikacja platformy ASP.NET jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/) z **ASP.NET i programowaniem w sieci Web** .
- Konto społecznościowe w serwisie Facebook, Google lub Microsoft.
- [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    W przykładowym rozwiązaniu są dwa projekty:

    - **TaskWebApp** — aplikacja internetowa, która pozwala tworzyć i edytować listę zadań. Aplikacja sieci Web używa przepływu użytkowników **rejestracji lub logowania** w celu rejestrowania lub logowania użytkowników.
    - **TaskService** — internetowy interfejs API, który obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Internetowy interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację internetową.

## <a name="run-the-application-in-visual-studio"></a>Uruchamianie aplikacji w programie Visual Studio

1. W folderze projektu przykładowej aplikacji otwórz rozwiązanie **B2C-WebAPI-DotNet.sln** w programie Visual Studio.
2. Na potrzeby tego przewodnika Szybki start uruchomisz równocześnie projekty **TaskWebApp** i **TaskService**. Kliknij prawym przyciskiem myszy rozwiązanie **B2C-WebAPI-DotNet** w Eksploratorze rozwiązań i wybierz pozycję **Ustaw projekty startowe**.
3. Wybierz pozycję **Wiele projektów startowych** i zmień **Akcję** dla obu projektów na **Uruchom**.
4. Kliknij przycisk **OK**.
5. Naciśnij klawisz **F5**, aby debugować obie aplikacje. Każda z aplikacji zostanie otwarta we własnej karcie przeglądarki:

    - `https://localhost:44316/` — aplikacja internetowa platformy ASP.NET. W przewodniku Szybki start ta aplikacja jest używana bezpośrednio.
    - `https://localhost:44332/` — internetowy interfejs API wywoływany przez internetową aplikację platformy ASP.NET.

## <a name="sign-in-using-your-account"></a>Logowanie się przy użyciu swojego konta

1. Kliknij pozycję **Sign up / Sign in** (Zarejestruj się / Zaloguj się) w aplikacji internetowej platformy ASP.NET, aby uruchomić przepływ pracy.

    ![Przykładowa aplikacja sieci Web ASP.NET w przeglądarce z wyróżnionym linkiem rejestracji/podpisywania](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    Przykład obsługuje kilka opcji rejestracji, w tym przy użyciu dostawcy tożsamości dla sieci społecznościowej, lub tworzenia konta lokalnego przy użyciu adresu e-mail. W tym przewodniku szybki start Użyj konta dostawcy tożsamości społecznościowej w serwisie Facebook, Google lub Microsoft.

2. Azure AD B2C przedstawia stronę logowania fikcyjnej firmy o nazwie Fabrikam dla przykładowej aplikacji sieci Web. Aby zarejestrować się przy użyciu dostawcy tożsamości dla sieci społecznościowej, kliknij przycisk dostawcy tożsamości, którego chcesz użyć.

    ![Strona logowania lub rejestracji przedstawiająca przyciski dostawcy tożsamości](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    Użytkownik uwierzytelnia się (loguje się) przy użyciu poświadczeń konta społecznościowego i autoryzuje aplikację do odczytywania informacji z konta społecznościowego. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto.

3. Zakończ proces logowania dla dostawcy tożsamości.

## <a name="edit-your-profile"></a>Edycja profilu

Usługa Azure Active Directory B2C zawiera funkcję umożliwiającą użytkownikom aktualizowanie profilów. Przykładowa aplikacja internetowa używa przepływu użytkownika profilu edycji usługi Azure AD B2C dla przepływu pracy.

1. Na pasku menu aplikacji kliknij nazwę profilu, a następnie wybierz pozycję **Edit profile** (Edytuj profil), aby edytować utworzony profil.

    ![Przykładowa aplikacja internetowa w przeglądarce z wyróżnionym linkiem Edytuj profil](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. W polu **Display name** zmień swoją nazwę wyświetlaną, a w polu **City** — miejscowość, a następnie kliknij przycisk **Continue** (Kontynuuj), aby zaktualizować profil.

    Zmienione dane są wyświetlane w prawej górnej części strony głównej aplikacji internetowej.

## <a name="access-a-protected-api-resource"></a>Uzyskiwanie dostępu do chronionego zasobu interfejsu API

1. Kliknij pozycję **Lista zadań do wykonania**, aby określić i zmodyfikować pozycje listy zadań do wykonania.

2. Wpisz tekst w polu tekstowym **Nowy element**. Kliknij pozycję **Dodaj**, aby wywołać chroniony internetowy interfejs API usługi Azure AD B2C, który dodaje pozycję listy zadań do wykonania.

    ![Przykładowa aplikacja internetowa w przeglądarce z dodawaniem elementu listy czynności do wykonania](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    Aplikacja internetowa platformy ASP.NET dołącza token dostępu usługi Azure AD do żądania skierowanego do chronionego zasobu internetowego interfejsu API w celu wykonania operacji na pozycjach listy zadań do wykonania użytkownika.

Pomyślnie użyto konta użytkownika usługi Azure AD B2C do wykonania autoryzowanego wywołania chronionego internetowego interfejsu API usługi Azure AD B2C.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne przewodniki Szybki start lub samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto przykładowej aplikacji ASP.NET do:

* Logowanie za pomocą niestandardowej strony logowania
* Zaloguj się przy użyciu dostawcy tożsamości społecznościowej
* Utwórz konto Azure AD B2C
* Wywoływanie internetowego interfejsu API chronionego przez Azure AD B2C

Wprowadzenie do tworzenia własnej dzierżawy usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)
