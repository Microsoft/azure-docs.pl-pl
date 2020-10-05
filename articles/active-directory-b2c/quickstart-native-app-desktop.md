---
title: 'Szybki Start: Konfigurowanie logowania dla aplikacji klasycznej'
titleSuffix: Azure AD B2C
description: W tym przewodniku Szybki Start Uruchom przykładową aplikację klasyczną WPF, która używa Azure Active Directory B2C, aby zapewnić logowanie do konta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78187308"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Szybki start: konfigurowanie logowania dla aplikacji klasycznej przy użyciu usługi Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) umożliwia zarządzanie tożsamościami w chmurze w celu zapewnienia ochrony aplikacji, firm i klientów. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie względem kont społecznościowych i firmowych za pomocą protokołów zgodnych z otwartymi standardami. W tym przewodniku Szybki start przykładowa aplikacja klasyczna platformy Windows Presentation Foundation (WPF) jest używana do logowania za pomocą dostawcy tożsamości społecznościowych i wywoływania chronionego internetowego interfejsu API usługi Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/) z **ASP.NET i programowaniem w sieci Web** .
- Konto społecznościowe w serwisie Facebook, Google lub Microsoft.
- [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) lub Sklonuj repozytorium [Azure-Samples/Active-Directory-B2C-dotnet-Desktop z usługi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Uruchamianie aplikacji w programie Visual Studio

1. W folderze projektu przykładowej aplikacji otwórz rozwiązanie **active-directory-b2c-wpf.sln** w programie Visual Studio.
2. Naciśnij klawisz **F5**, aby debugować aplikację.

## <a name="sign-in-using-your-account"></a>Logowanie się przy użyciu swojego konta

1. Kliknij pozycję **Zaloguj**, aby uruchomić przepływ pracy **Rejestrowanie lub logowanie**.

    ![Zrzut ekranu przykładowej aplikacji WPF](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    Przykład obsługuje kilka opcji rejestracji. Te opcje obejmują użycie dostawcy tożsamości społecznościowej lub utworzenie konta lokalnego przy użyciu adresu e-mail. W tym przewodniku szybki start Użyj konta dostawcy tożsamości społecznościowej w serwisie Facebook, Google lub Microsoft.


2. Azure AD B2C przedstawia stronę logowania fikcyjnej firmy o nazwie Fabrikam dla przykładowej aplikacji sieci Web. Aby zarejestrować się przy użyciu dostawcy tożsamości dla sieci społecznościowej, kliknij przycisk dostawcy tożsamości, którego chcesz użyć.

    ![Strona logowania lub rejestracji przedstawiająca dostawców tożsamości](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Użytkownik uwierzytelnia się (loguje się) przy użyciu poświadczeń konta społecznościowego i autoryzuje aplikację do odczytywania informacji z konta społecznościowego. Po udzieleniu dostępu aplikacji może ona pobrać informacje z profilu na koncie w sieci społecznościowej, takie jak Twoje nazwisko i miasto.

2. Zakończ proces logowania dla dostawcy tożsamości.

    Szczegóły profilu na nowym koncie są wstępnie wypełnione informacjami z konta w sieci społecznościowej.

## <a name="edit-your-profile"></a>Edycja profilu

Usługa Azure AD B2C zawiera funkcję umożliwiającą użytkownikom aktualizowanie profilów. Przykładowa aplikacja internetowa używa przepływu użytkownika profilu edycji usługi Azure AD B2C dla przepływu pracy.

1. Na pasku menu aplikacji kliknij pozycję **Edit profile** (Edytuj profil), aby edytować utworzony profil.

    ![Przycisk Edytuj profil wyróżniony w przykładowej aplikacji WPF](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Wybierz dostawcę tożsamości skojarzonego z utworzonym kontem. Jeśli na przykład podczas tworzenia konta użyto usługi Facebook jako dostawcy tożsamości, wybierz pozycję Facebook, aby zmodyfikować szczegóły skojarzonego profilu.

3. W polu **Display name** zmień swoją nazwę wyświetlaną, a w polu **City** — miejscowość, a następnie kliknij przycisk **Continue** (Kontynuuj).

    Nowy token dostępu zostanie wyświetlony w polu tekstowym *Informacje o tokenie*. Jeśli chcesz zweryfikować zmiany profilu, skopiuj i wklej token dostępu do dekodera tokenów na stronie https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Uzyskiwanie dostępu do chronionego zasobu interfejsu API

Kliknij pozycję **Wywołaj interfejs API**, aby wysłać żądanie do chronionego zasobu.

![Wywoływanie interfejsu API](./media/quickstart-native-app-desktop/call-api-wpf.png)

Aplikacja dołącza token dostępu usługi Azure AD do żądania wysłanego do chronionego zasobu internetowego interfejsu API. Internetowy interfejs API odsyła z powrotem nazwę wyświetlaną zawartą w tokenie dostępu.

Pomyślnie użyto konta użytkownika usługi Azure AD B2C do wykonania autoryzowanego wywołania chronionego internetowego interfejsu API usługi Azure AD B2C.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne przewodniki Szybki start lub samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto przykładowej aplikacji klasycznej do:

* Logowanie za pomocą niestandardowej strony logowania
* Zaloguj się przy użyciu dostawcy tożsamości społecznościowej
* Utwórz konto Azure AD B2C
* Wywoływanie internetowego interfejsu API chronionego przez Azure AD B2C

Wprowadzenie do tworzenia własnej dzierżawy usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Tworzenie dzierżawy usługi Azure Active Directory B2C w witrynie Azure Portal](tutorial-create-tenant.md)
