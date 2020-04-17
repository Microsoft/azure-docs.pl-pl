---
title: Dodawanie logowania za pomocą firmy Microsoft do aplikacji sieci Web Python platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zaimplementować logowanie microsoftu w aplikacji sieci Web języka Python przy użyciu usługi OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: c0220a226b2095a4ec54bc3469abee08b8b29f4c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536033"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Szybki start: dodawanie logowania za pomocą firmy Microsoft do aplikacji sieci Web języka Python

W tym przewodniku Szybki start dowiesz się, jak zintegrować aplikację sieci Web języka Python z platformą tożsamości firmy Microsoft. Aplikacja zaloguje się do użytkownika, pobierze token dostępu, aby wywołać interfejs API programu Microsoft Graph i zażąda interfejsu API programu Microsoft Graph.

Po zakończeniu przewodnika aplikacja będzie akceptować logowania osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych lub szkolnych od dowolnej firmy lub organizacji korzystającej z usługi Azure Active Directory. (Zobacz [Jak działa przykład](#how-the-sample-works) dla ilustracji).


## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten przykład, należy:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) lub [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Kolby](http://flask.pocoo.org/), [Flask-Sesja](https://pythonhosted.org/Flask-Session/), [wnioski](https://requests.kennethreitz.org/en/master/)
- [MĘTÓW MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
>
> Dostępne są dwie opcje uruchomienia aplikacji szybki start: ekspresowa (opcja 1) i ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [witryny Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Wybierz **pozycję Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>      - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `python-webapp`.
>      - W obszarze **Obsługiwane typy kont** wybierz pozycję **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft**.
>      - Wybierz pozycję **Zarejestruj**.
>      - Na stronie **Przegląd** aplikacji zanotuj wartość **identyfikatora aplikacji (klienta)** do późniejszego użycia.
> 1. Wybierz **uwierzytelnianie** z menu, a następnie dodaj następujące informacje:
>    - Dodaj konfigurację platformy **sieci Web.** Dodaj `http://localhost:5000/getAToken` jako **przekierowanie identyfikatorów URI**.
>    - Wybierz pozycję **Zapisz**.
> 1. W menu po lewej stronie wybierz **certyfikaty & wpisy tajne** i kliknij **nowy klucz tajny klienta** w sekcji **Tajemnice klienta:**
>
>      - Wpisz opis klucza (klucz tajny aplikacji instancji).
>      - Wybierz kluczowy czas trwania **w ciągu 1 roku**.
>      - Po kliknięciu przycisku **Dodaj**zostanie wyświetlona wartość klucza.
>      - Skopiuj wartość klucza. Będzie potrzebny później.
> 1. Wybierz sekcję **Uprawnienia interfejsu API**
>
>      - Kliknij przycisk **Dodaj uprawnienie,** a następnie
>      - Upewnij się, że jest zaznaczona karta **Interfejsy API** firmy Microsoft
>      - W sekcji *Często używane interfejsy API firmy Microsoft* kliknij pozycję Microsoft **Graph**
>      - W sekcji **Uprawnienia delegowane** upewnij się, że zaznaczone są odpowiednie uprawnienia: **User.ReadBasic.All**. W razie potrzeby użyj pola wyszukiwania.
>      - Wybierz przycisk **Dodaj uprawnienia**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
>
> Aby przykładowy kod dla tego szybkiego startu działał, musisz:
>
> 1. Dodaj adres URL `http://localhost:5000/getAToken`odpowiedzi jako .
> 1. Utwórz klucz tajny klienta.
> 1. Dodaj user.ReadBasic.All delegowane uprawnienie interfejsu API programu Microsoft Graph.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-aspnet-webapp/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tego atrybutu

#### <a name="step-2-download-your-project"></a>Krok 2. Pobieranie projektu
> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Pobierz projekt i wyodrębnij plik zip do folderu lokalnego bliżej folderu głównego - na przykład **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Krok 3: Konfigurowanie aplikacji
>
> 1. Wyodrębnij plik zip do folderu lokalnego bliższego folderowi głównemu, na przykład **C:\Azure-Samples**
> 1. Jeśli używasz zintegrowanego środowiska programistycznego, otwórz próbkę w swoim ulubionym ŚRODOWISKU IDE (opcjonalnie).
> 1. Otwórz plik **app_config.py,** który można znaleźć w folderze głównym i zastąp następującym fragmentem kodu:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Gdzie:
>
> - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
> - `Enter_the_Client_Secret_Here`- jest **kluczem tajnym klienta** utworzonym w **certyfikatach & tajemnic** dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Name_Here`- jest wartością **identyfikatora katalogu (dzierżawy)** zarejestrowanej aplikacji.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: Uruchom przykładowy kod

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: Uruchom przykładowy kod

1. Należy zainstalować bibliotekę MSAL Python, strukturę flask, sesje flask do zarządzania sesjami po stronie serwera i żądania przy użyciu pip w następujący sposób:

    ```Shell
    pip install -r requirements.txt
    ```

2. Uruchom app.py z powłoki lub wiersza polecenia:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst w plikach projektu, ze względów bezpieczeństwa zaleca się używanie certyfikatu zamiast klucza tajnego klienta, zanim będzie można uznać aplikację za produkcyjną. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [te instrukcje](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa próbka
![Pokazuje, jak działa przykładowa aplikacja generowana przez ten szybki start](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Uzyskiwanie msal
MSAL to biblioteka używana do logowania użytkowników i żądania tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.
Można dodać MSAL Python do aplikacji za pomocą Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL
Odwołanie do programu MSAL Python można dodać, dodając następujący kod do górnej części pliku, w którym będzie używany msal:

```Python
import msal
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o aplikacjach sieci Web, które logują się do użytkowników, a następnie wywołuje internetowe interfejsy API:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacje sieci Web, które logują się do użytkowników](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
