---
title: 'Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja sieci Web w języku Python może zalogować użytkowników, uzyskać token dostępu z platformy tożsamości firmy Microsoft i wywołać interfejs API Microsoft Graph.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 383f7f37e93b4705419ba1f93f509c86eaab192b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030641"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python

W tym przewodniku szybki start pobrano i uruchomiono przykład kodu, który pokazuje, jak aplikacja sieci Web w języku Python może zalogować użytkowników i uzyskać token dostępu do wywołania interfejsu API Microsoft Graph. Użytkownicy korzystający z osobistego konta Microsoft lub konta w dowolnej organizacji Azure Active Directory (Azure AD) mogą zalogować się do aplikacji.

Zobacz [, jak działa Przykładowa](#how-the-sample-works) ilustracja.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) lub [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Kolba](http://flask.pocoo.org/), [podsesja](https://pypi.org/project/Flask-Session/), [żądania](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
>
> Dostępne są dwie opcje uruchomienia aplikacji szybkiego startu: Express (opcja 1) i ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [Rejestracje aplikacji Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. W obszarze **Zarządzaj** wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład `python-webapp` . Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę i można ją później zmienić.
> 1. W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. Na stronie **Przegląd** aplikacji Zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. Wybierz pozycję **Dodaj platformę**  >  **sieci Web**.
> 1. Dodaj `http://localhost:5000/getAToken` jako **identyfikatory URI przekierowania**.
> 1. Wybierz pozycję **Konfiguruj**.
> 1. W obszarze **Zarządzaj** wybierz pozycję **Certyfikaty & wpisy tajne**  i w sekcji **tajne klienta** wybierz pozycję **nowy klucz tajny** klienta.
> 1. Wpisz opis klucza (dla przykładowego wpisu tajnego aplikacji), Pozostaw domyślne wygaśnięcie, a następnie wybierz pozycję **Dodaj**.
> 1. Zwróć uwagę na **wartość** **klucza tajnego klienta** do późniejszego użycia.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uprawnienia interfejsu API**  >  **Dodaj uprawnienie**.
>1.  Upewnij się, że wybrano kartę **interfejsy API firmy Microsoft** .
> 1. W sekcji *najczęściej używane interfejsy API firmy Microsoft* wybierz pozycję **Microsoft Graph**.
> 1. W sekcji **uprawnienia delegowane** upewnij się, że są zaznaczone odpowiednie uprawnienia: **User. ReadBasic. All**. W razie potrzeby użyj pola wyszukiwania.
> 1. Wybierz przycisk **Dodaj uprawnienia** .
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
>
> Aby uzyskać przykładowy kod dla tego przewodnika Szybki Start, należy wykonać następujące czynności:
>
> 1. Dodaj adres URL odpowiedzi jako `http://localhost:5000/getAToken` .
> 1. Utwórz klucz tajny klienta.
> 1. Microsoft Graph Dodaj uprawnienie ReadBasic użytkownika interfejsu API. wszystkie delegowane uprawnienia.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-aspnet-webapp/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tego atrybutu

#### <a name="step-2-download-your-project"></a>Krok 2. Pobieranie projektu
> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Pobierz projekt i wyodrębnij plik zip do folderu lokalnego bliżej folderu głównego — na przykład **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Krok 3. Konfigurowanie aplikacji
>
> 1. Wyodrębnij plik zip do folderu lokalnego bliższego folderowi głównemu, na przykład **C:\Azure-Samples**
> 1. Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w ulubionym środowisku IDE (opcjonalnie).
> 1. Otwórz plik **app_config. PR** , który znajduje się w folderze głównym i Zamień na następujący fragment kodu:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Gdzie:
>
> - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
> - `Enter_the_Client_Secret_Here` -jest **kluczem tajnym klienta** utworzonym w **certyfikatach & wpisy tajne**  dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Name_Here` -jest wartością **identyfikatora katalogu (dzierżawcy)** zarejestrowanej aplikacji.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3. Uruchamianie przykładu kodu

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4. Uruchamianie przykładu kodu

1. Konieczne będzie zainstalowanie biblioteki MSAL Python, architektury kolb Flask-Sessions do zarządzania sesjami po stronie serwera i żądań przy użyciu funkcji PIP w następujący sposób:

    ```Shell
    pip install -r requirements.txt
    ```

2. Uruchom app.py z powłoki lub wiersza polecenia:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst w plikach projektu, ze względów bezpieczeństwa zaleca się używanie certyfikatu zamiast klucza tajnego klienta, zanim będzie można uznać aplikację za produkcyjną. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [te instrukcje](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład
![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Pobieranie MSAL
MSAL to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.
Możesz dodać MSAL Python do aplikacji przy użyciu narzędzia PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL
Można dodać odwołanie do MSAL Python, dodając następujący kod na początku pliku, w którym będzie używany MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat aplikacji sieci Web, które logują użytkowników w naszej wieloczęściowej serii scenariuszy.

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md)
