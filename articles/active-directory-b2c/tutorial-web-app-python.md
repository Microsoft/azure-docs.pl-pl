---
title: 'Samouczek: Włączanie uwierzytelniania w aplikacji sieci Web w języku Python'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak za pomocą Azure Active Directory B2C zapewnić Logowanie użytkownika dla aplikacji sieci Web kolby języka Python.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555678"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Samouczek: Włączanie uwierzytelniania w aplikacji sieci Web w języku Python za pomocą Azure AD B2C

W tym samouczku pokazano, jak używać programu Azure Active Directory B2C (Azure AD B2C) do rejestrowania i logowania użytkowników w aplikacji sieci Web kolby języka Python.

W tym samouczku:

> [!div class="checklist"]
> * Dodawanie adresu URL odpowiedzi do aplikacji zarejestrowanej w dzierżawie Azure AD B2C
> * Pobierz przykład kodu z usługi GitHub
> * Modyfikowanie kodu przykładowej aplikacji w celu współdziałania z dzierżawcą
> * Utwórz konto za pomocą przepływu użytkownika dotyczącego rejestracji/logowania

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku potrzebne są następujące Azure AD B2C zasoby:

* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)
* [Aplikacja zarejestrowana](tutorial-register-applications.md) w dzierżawie oraz jej *Identyfikator aplikacji (klienta)* i *klucz tajny klienta*
* [Przepływy użytkowników utworzone](tutorial-create-user-flows.md) w dzierżawie

Ponadto w lokalnym środowisku programistycznym są potrzebne następujące elementy:

* [Visual Studio Code](https://code.visualstudio.com/) lub inny edytor kodu
* [Python](https://nodejs.org/en/download/) 2.7 + lub 3 +

## <a name="add-a-redirect-uri"></a>Dodaj identyfikator URI przekierowania

W drugim samouczku, który został ukończony w ramach wymagań wstępnych, zarejestrowano aplikację sieci Web w Azure AD B2C. Aby włączyć komunikację z przykładem kodu w tym samouczku, Dodaj adres URL odpowiedzi (nazywany także identyfikatorem URI przekierowania) do rejestracji aplikacji.

Aby zaktualizować aplikację w dzierżawie Azure AD B2C, możesz użyć naszego nowego środowiska **rejestracje aplikacji** lub naszych starszych  **aplikacji (starsza wersja)** . [Dowiedz się więcej na temat nowego środowiska](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, wybierz kartę **posiadane aplikacje** , a następnie wybierz aplikację *webapp1* .
1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
1. W obszarze **Sieć Web** wybierz łącze **Dodaj identyfikator URI** , a następnie wprowadź `http://localhost:5000/getAToken` w polu tekstowym.
1. Wybierz pozycję **Zapisz**.

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **aplikacje (starsza wersja)**, a następnie wybierz aplikację *webapp1* .
1. W obszarze **Adres URL odpowiedzi** dodaj `http://localhost:5000/getAToken`.
1. Wybierz pozycję **Zapisz**.
* * *

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

W tym samouczku skonfigurujesz przykładowy kod pobrany z usługi GitHub do pracy z dzierżawcą B2C. Przykład demonstruje, jak aplikacja sieci Web kolby Python może używać Azure AD B2C do tworzenia konta i logowania użytkowników.

[Pobierz. Archiwum ZIP](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) lub klonowanie [repozytorium przykładowego kodu](https://github.com/Azure-Samples/ms-identity-python-webapp) z usługi GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Aktualizowanie przykładu

Po uzyskaniu przykładu Skonfiguruj aplikację tak, aby korzystała z dzierżawy Azure AD B2C, rejestracji aplikacji i przepływów użytkowników.

W katalogu głównym projektu:

1. Zmień nazwę pliku *app_config. PR* na *app_config. pr. old*
1. Zmień nazwę pliku *app_config_b2c. PR* na *app_config. PR*

Zaktualizuj nowo zmieniono nazwę *app_config. PR* o wartości dla dzierżawy Azure AD B2C i rejestracji aplikacji utworzonej w ramach wymagań wstępnych.

1. Otwórz plik *app_config. PR* w edytorze.
1. Zaktualizuj `b2c_tenant` wartość o nazwę dzierżawy Azure AD B2C, na przykład *contosob2c*.
1. Zaktualizuj każdą z `*_user_flow` wartości, aby dopasować nazwy przepływów użytkownika utworzonych w ramach wymagań wstępnych.
1. Zaktualizuj `CLIENT_ID` wartość za pomocą **identyfikatora aplikacji** sieci Web, która została zarejestrowana w ramach wymagań wstępnych.
1. Zaktualizuj `CLIENT_SECRET` wartość przy użyciu wartości **klucza tajnego klienta** utworzonego w sekcji wymagania wstępne. W celu zwiększenia bezpieczeństwa Rozważ przechowywanie go zamiast tego w **zmiennej środowiskowej** zgodnie z zaleceniami w komentarzach.

Górna sekcja *app_config. PR* powinna wyglądać podobnie do poniższego fragmentu kodu:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Jak wskazano w komentarzach fragmentu kodu, firma Microsoft zaleca, aby **nie przechowywać wpisów tajnych w** kodzie aplikacji. Zmienna stałe jest używana w przykładowym kodzie wyłącznie dla *wygody*. Rozważ użycie zmiennej środowiskowej lub magazynu wpisów tajnych, takiego jak Azure Key Vault.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. W konsoli lub terminalu przejdź do katalogu zawierającego przykład. Na przykład:

    ```console
    cd ms-identity-python-webapp
    ```
1. Uruchom następujące polecenia, aby zainstalować wymagane pakiety z PyPi i uruchomić aplikację sieci Web na komputerze lokalnym:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    W oknie konsoli jest wyświetlany numer portu uruchomionej lokalnie aplikacji:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Przejdź do strony, aby `http://localhost:5000` wyświetlić aplikację sieci Web działającą na komputerze lokalnym.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Przeglądarka sieci Web, w której jest uruchomiona lokalna aplikacja sieci Web z kolbą Python":::

### <a name="sign-up-using-an-email-address"></a>Rejestrowanie się przy użyciu adresu e-mail

Ta przykładowa aplikacja obsługuje rejestrowanie, logowanie i resetowanie hasła. W tym samouczku utworzysz konto przy użyciu adresu e-mail.

1. Wybierz pozycję **Zaloguj** , aby zainicjować przepływ użytkownika *B2C_1_signupsignin1* określonego we wcześniejszym kroku.
1. Azure AD B2C przedstawia stronę logowania, która zawiera link do rejestracji. Ponieważ jeszcze nie masz konta, wybierz link **Utwórz konto teraz** .
1. Przepływ pracy tworzenia konta przedstawia stronę do zbierania i weryfikowania tożsamości użytkownika przy użyciu adresu e-mail. Przepływ pracy rejestracji również zbiera hasło użytkownika i wymagane atrybuty zdefiniowane w przepływie użytkownika.

    Użyj prawidłowego adresu e-mail i przeprowadź weryfikację przy użyciu kodu weryfikacyjnego. Ustaw hasło. Wprowadź wartości żądanych atrybutów.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Strona rejestracji wyświetlana przez Azure AD B2C przepływ użytkownika":::

1. Wybierz pozycję **Utwórz** , aby utworzyć konto lokalne w katalogu Azure AD B2C.

Po wybraniu opcji **Utwórz** aplikacja wyświetli nazwę zalogowanego użytkownika.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Przeglądarka sieci Web przedstawiająca aplikację sieci Web w języku Python z zalogowanym użytkownikiem":::

Jeśli chcesz przetestować logowanie, wybierz łącze **Wyloguj** , a następnie wybierz pozycję **Zaloguj** się i zaloguj się przy użyciu adresu e-mail i hasła wprowadzonego podczas rejestracji.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano aplikację sieci Web kolby Python do pracy z przepływem użytkownika w dzierżawie Azure AD B2C, aby zapewnić możliwość tworzenia konta i logowania. Wykonano następujące kroki:

> [!div class="checklist"]
> * Dodano adres URL odpowiedzi do aplikacji zarejestrowanej w dzierżawie Azure AD B2C
> * Pobieranie przykładu kodu z usługi GitHub
> * Zmodyfikowano kod przykładowej aplikacji do pracy z dzierżawcą
> * Rejestracja przy użyciu przepływu użytkownika dotyczącego rejestracji/logowania

Następnie Dowiedz się, jak dostosować interfejs użytkownika stron przepływu użytkownika wyświetlanych użytkownikom przez Azure AD B2C:

> [!div class="nextstepaction"]
> [Dostosowywanie interfejsu środowiska użytkownika w Azure AD B2C >](customize-ui.md)