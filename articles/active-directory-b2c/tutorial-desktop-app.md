---
title: 'Samouczek: uwierzytelnianie użytkowników w natywnej aplikacji klienckiej'
titleSuffix: Azure AD B2C
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji klasycznej platformy .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78186203"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Samouczek: uwierzytelnianie użytkowników w natywnym kliencie klasycznym przy użyciu Azure Active Directory B2C

W tym samouczku pokazano, jak używać programu Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w aplikacji klasycznej Windows Presentation Foundation (WPF). Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji.
- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) przy użyciu programu **.NET desktop Development** i **ASP.NET oraz obciążeń związanych z programowaniem aplikacji sieci Web** .

## <a name="add-the-native-client-application"></a>Dodawanie natywnej aplikacji klienckiej

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. Przykładowa aplikacja klasyczna WPF pokazuje możliwość rejestrowania się, logowania i wywoływania chronionego internetowego interfejsu API w Azure AD B2C. [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [przejrzyj repozytorium](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) lub sklonuj przykład z witryny GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Aby zaktualizować aplikację do pracy z dzierżawą Azure AD B2C i wywoływać przepływy użytkowników zamiast tych w domyślnej dzierżawie demonstracyjnej:

1. Otwórz rozwiązanie **Active-Directory-B2C-WPF** ( `active-directory-b2c-wpf.sln` ) w programie Visual Studio.
2. W projekcie **Active-Directory-B2C-WPF** otwórz plik *App.XAML.cs* i Znajdź następujące definicje zmiennych. Zamień `{your-tenant-name}` na nazwę dzierżawy Azure AD B2C i `{application-ID}` Identyfikator aplikacji, który został zarejestrowany wcześniej.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Zaktualizuj zmienne nazw zasad o nazwy przepływów użytkownika, które zostały utworzone w ramach wymagań wstępnych. Na przykład:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Naciśnij klawisz **F5**, aby skompilować i uruchomić przykładową aplikację.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Wybierz pozycję **Zaloguj** się, aby zarejestrować się jako użytkownik. Jest tu używany przepływ użytkownika **B2C_1_signupsignin1**.
2. Azure AD B2C przedstawia stronę logowania za pomocą linku **Utwórz konto teraz** . Ponieważ jeszcze nie masz konta, wybierz link **Utwórz konto teraz** .
3. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji wyświetlana w ramach przepływu pracy logowania/rejestrowania](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Wybierz pozycję **Utwórz** , aby utworzyć konto lokalne w dzierżawie Azure AD B2C.

Użytkownik może teraz zalogować się i korzystać z aplikacji klasycznej przy użyciu swojego adresu e-mail. Po pomyślnym zarejestrowaniu lub zalogowaniu szczegóły tokenu są wyświetlane w dolnym okienku aplikacji WPF.

![Szczegóły tokenu wyświetlane w dolnym okienku aplikacji klasycznej WPF](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

W przypadku wybrania przycisku **interfejsu API wywołania** zostanie wyświetlony **komunikat o błędzie** . Wystąpił błąd, ponieważ w bieżącym stanie aplikacja próbuje uzyskać dostęp do interfejsu API chronionego przez dzierżawę demonstracyjną `fabrikamb2c.onmicrosoft.com` . Ponieważ token dostępu jest prawidłowy tylko dla dzierżawy Azure AD B2C, wywołanie interfejsu API jest dlatego nieautoryzowane.

Przejdź do następnego samouczka, aby zarejestrować chroniony internetowy interfejs API we własnej dzierżawie i włączyć funkcję **interfejsu API wywołania** .

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie natywnej aplikacji klienckiej
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Następnie, aby włączyć funkcję **interfejsu API wywołania** , udziel aplikacji klasycznej WPF dostęp do interfejsu API sieci Web zarejestrowanego we własnej dzierżawie Azure AD B2C:

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu do Node.js internetowego interfejsu API z poziomu aplikacji klasycznej >](tutorial-desktop-app-webapi.md)
