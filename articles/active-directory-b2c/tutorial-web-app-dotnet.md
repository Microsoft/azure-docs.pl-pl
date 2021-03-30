---
title: 'Samouczek: Włączanie uwierzytelniania w aplikacji sieci Web'
titleSuffix: Azure AD B2C
description: Samouczek dotyczący sposobu użycia usługi Azure Active Directory B2C w celu określenia nazwy logowania użytkownika na potrzeby aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953036"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Samouczek: Włączanie uwierzytelniania w aplikacji sieci Web przy użyciu Azure Active Directory B2C

W tym samouczku pokazano, jak używać programu Azure Active Directory B2C (Azure AD B2C) do logowania się i rejestrowania użytkowników w aplikacji sieci Web ASP.NET. Usługa Azure AD B2C umożliwia aplikacjom uwierzytelnianie się na kontach społecznościowych, kontach przedsiębiorstw i kontach usługi Azure Active Directory za pomocą otwartych standardowych protokołów.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> W tym samouczku jest stosowana Przykładowa aplikacja sieci Web ASP.NET. Aby poznać inne przykładowe aplikacje (w tym ASP.NET Core, Node.js, Python i innych), zobacz [Azure Active Directory B2C przykładów kodu](code-samples.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie przepływów użytkownika](tutorial-create-user-flows.md), aby umieścić platformę użytkownika w Twojej aplikacji.
* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) przy użyciu obciążeń **ASP.NET i Web Development** .

## <a name="update-the-application-registration"></a>Aktualizowanie rejestracji aplikacji

W samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci Web w Azure AD B2C. Aby włączyć komunikację z przykładem w tym samouczku, musisz dodać identyfikator URI przekierowania i utworzyć klucz tajny klienta (klucz) dla zarejestrowanej aplikacji.

### <a name="add-a-redirect-uri-reply-url"></a>Dodawanie identyfikatora URI przekierowania (adres URL odpowiedzi)

Aby zaktualizować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, wybierz kartę **posiadane aplikacje** , a następnie wybierz aplikację *webapp1* .
1. W obszarze **Sieć Web** wybierz łącze **Dodaj identyfikator URI** , wprowadź `https://localhost:44316` , a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Omówienie**.
1. Rejestrowanie **identyfikatora aplikacji (klienta)** do użycia w późniejszym kroku podczas konfigurowania aplikacji sieci Web.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz aplikację *webapp1* .
1. W obszarze **Adres URL odpowiedzi** dodaj `https://localhost:44316`.
1. Wybierz pozycję **Zapisz**.
1. Na stronie właściwości Zapisz identyfikator aplikacji do użycia w późniejszym kroku podczas konfigurowania aplikacji sieci Web.

* * *

### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Następnie Utwórz klucz tajny klienta dla zarejestrowanej aplikacji sieci Web. Przykładowy kod aplikacji sieci Web używa tego, aby potwierdzić swoją tożsamość podczas żądania tokenów.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

W tym samouczku skonfigurujesz przykładową aplikację, którą możesz pobrać z witryny GitHub. W przykładowej aplikacji użyto platformy ASP.NET, aby udostępnić prostą listę zadań do wykonania. W przykładowej aplikacji użyto [składników oprogramowania pośredniczącego Microsoft OWIN](/aspnet/aspnet/overview/owin-and-katana/). [Pobierz plik ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) lub sklonuj przykład z usługi GitHub. Upewnij się, że przykładowy plik wyodrębniono w folderze, którego łączna liczba znaków ścieżki jest mniejsza niż 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Przykładowe rozwiązanie zawiera następujące dwa projekty:

* **TaskWebApp** — tworzenie i edytowanie listy zadań. Przykład korzysta z przepływu logowania **lub logowania** użytkownika w celu rejestracji i logowania użytkowników.
* **TaskService** — obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację TaskWebApp.

Należy zmienić przykładową aplikację tak, aby korzystała z aplikacji zarejestrowanej w Twojej dzierżawie. Obejmuje to zanotowane wcześniej identyfikator aplikacji i klucz. Skonfiguruj też utworzone przepływy użytkownika. Przykład definiuje wartości konfiguracji jako ustawienia w pliku *Web.config* .

Zaktualizuj ustawienia w pliku Web.config, aby współdziałać z przepływem użytkownika:

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.
1. W projekcie **TaskWebApp** otwórz plik **Web.config**.
    1. Zaktualizuj wartość `ida:Tenant` i `ida:AadInstance` o nazwę utworzonej dzierżawy Azure AD B2C. Na przykład Zastąp ciąg `fabrikamb2c` opcją `contoso` .
    1. Zastąp wartość `ida:TenantId` identyfikatorem katalogu, który można znaleźć we właściwościach dzierżawy usługi Azure B2C (w obszarze Azure Portal w obszarze właściwości **Azure Active Directory**  >    >  **Identyfikator katalogu**).
    1. Zastąp wartość `ida:ClientId` identyfikatorem aplikacji, która została zarejestrowana.
    1. Zastąp wartość elementu `ida:ClientSecret` zanotowanym kluczem. Jeśli wpis tajny klienta zawiera wszystkie wstępnie zdefiniowane jednostki XML, na przykład mniejsze niż ( `<` ), większe niż () `>` , handlowe "i" (), `&` lub podwójne cudzysłowy ( `"` ), należy wypróbować te znaki przez Kodowanie XML przed dodaniem go do Web.config.
    1. Zastąp wartość wartością `ida:SignUpSignInPolicyId` `b2c_1_signupsignin1` .
    1. Zastąp wartość wartością `ida:EditProfilePolicyId` `b2c_1_profileediting1` .
    1. Zastąp wartość wartością `ida:ResetPasswordPolicyId` `b2c_1_passwordreset1` .

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt **TaskWebApp**, a następnie kliknij pozycję **Ustaw jako projekt startowy**.
1. Naciśnij klawisz **F5**. Zostanie uruchomiona domyślna przeglądarka z adresem lokalnej witryny internetowej `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

1. Wybierz pozycję **Utwórz konto/Zaloguj** się, aby zarejestrować się jako użytkownik aplikacji. Jest używany przepływ użytkownika **b2c_1_signupsignin1**.
1. Usługa Azure AD B2C wyświetli stronę logowania z linkiem rejestracji. Ponieważ nie masz jeszcze konta, wybierz pozycję **Zarejestruj się teraz**. W przepływie pracy rejestracji jest wyświetlana strona do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji zbiera też hasło użytkownika i żądane atrybuty zdefiniowane w przepływie użytkownika.
1. Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    ![Strona rejestracji wyświetlana w ramach przepływu pracy logowania/rejestrowania](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Wybierz pozycję **Utwórz** , aby utworzyć konto lokalne w dzierżawie Azure AD B2C.

Użytkownik aplikacji może teraz zalogować się i korzystać z aplikacji sieci Web przy użyciu swojego adresu e-mail.

Jednak funkcja **listy czynności do wykonania** nie będzie działać do momentu zakończenia następnego samouczka w serii, [samouczek: Użyj Azure AD B2C, aby chronić internetowy interfejs API ASP.NET](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Aktualizowanie aplikacji w usłudze Azure AD B2C
> * Konfigurowanie przykładu korzystania z aplikacji
> * Rejestrowanie przy użyciu przepływu użytkownika

Teraz przejdź do następnego samouczka, aby włączyć funkcję **Lista czynności do wykonania** w aplikacji sieci Web. W tym celu należy zarejestrować aplikację internetowego interfejsu API we własnej Azure AD B2C dzierżawie, a następnie zmodyfikować przykład kodu, aby użyć dzierżawy do uwierzytelniania interfejsu API.

> [!div class="nextstepaction"]
> [Samouczek: używanie Azure Active Directory B2C do ochrony interfejsu API sieci Web ASP.NET >](tutorial-web-api-dotnet.md)