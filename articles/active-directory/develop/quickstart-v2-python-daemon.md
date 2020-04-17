---
title: Demon Pythona platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak proces języka Python może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy platformy tożsamości firmy Microsoft przy użyciu własnej tożsamości aplikacji
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.openlocfilehash: 3c6cb6303734b5336b3e9a7646e5eb3310d0f236
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536050"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z aplikacji konsoli Języka Python przy użyciu tożsamości aplikacji

W tym przewodniku Szybki start napisz aplikację języka Python, która pobiera token dostępu przy użyciu tożsamości aplikacji, a następnie wywołuje interfejs API programu Microsoft Graph, aby wyświetlić [listę użytkowników](https://docs.microsoft.com/graph/api/user-list) w katalogu. Ten scenariusz przydaje się w sytuacjach, które wymagają uruchamiania bezobsługowego, nienadzorowanego zadania lub usługi systemu Windows przy użyciu tożsamości aplikacji, a nie tożsamości użytkownika.

> [!div renderon="docs"]
> ![Pokazuje, jak działa przykładowa aplikacja generowana przez ten szybki start](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten przykład, potrzebujesz:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) lub [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MĘTÓW MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start

> [!div renderon="docs" class="sxs-lookup"]
>
> Dostępne są dwie opcje uruchomienia aplikacji Szybki start: Express (opcja 1 poniżej) i Manual (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego okienka [Azure portal — rejestracje aplikacji.](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs)
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Wybierz **pozycję Nowa rejestracja**.
> 1. Po **wyświetleniu** strony Zarejestruj zgłoszenie wprowadź informacje rejestracyjne aplikacji.
> 1. W sekcji **Nazwa** podaj zrozumiałą nazwę aplikacji, która będzie widoczna dla użytkowników, na przykład `Daemon-console`, a następnie wybierz pozycję **Zarejestruj**, aby utworzyć aplikację.
> 1. Po jej zarejestrowaniu wybierz menu **Certyfikaty i klucze tajne**.
> 1. W obszarze **Klucze tajne klienta** wybierz pozycję **+ Nowy klucz tajny klienta**. Nadaj kluczowi nazwę i wybierz pozycję **Dodaj**. Skopiuj klucz tajny do bezpiecznej lokalizacji. Będzie on używany w kodzie.
> 1. Wybierz menu **Uprawnienia interfejsu API**, wybierz przycisk **+ Dodaj uprawnienie**, a następnie wybierz pozycję **Microsoft Graph**.
> 1. Wybierz pozycję **Uprawnienia aplikacji**.
> 1. W węźle **Użytkownik** wybierz pozycję **User.Read.All**, a następnie wybierz pozycję **Dodaj uprawnienia**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Pobieranie i konfigurowanie aplikacji Szybki Start
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby działał przykładowy kod z tego przewodnika Szybki start, musisz utworzyć klucz tajny klienta i dodać uprawnienie aplikacji **User.Read.All** interfejsu API programu Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-netcore-daemon/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-python-project"></a>Krok 2: Pobierz swój projekt Pythona

> [!div renderon="docs"]
> [Pobierz projekt demona Pythona](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Krok 3: Konfigurowanie projektu Języka Python
>
> 1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
> 1. Przejdź do podfolderu **1-Call-MsGraph-WithSecret"**.
> 1. Edytuj **plik parameters.json** i zastąp wartości pól `authority`i `client_id` `secret` następującym fragmentem kodu:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Gdzie:
>    - `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
>    - `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here` — zastąp tę wartość kluczem tajnym klienta utworzonym w kroku 1.
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)**, przejdź do strony **Przegląd** aplikacji w witrynie Azure Portal. Aby wygenerować nowy klucz, przejdź do strony **Certyfikaty i klucze tajne**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3: Zgoda administratora

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4. Zgoda administratora

Jeśli spróbujesz uruchomić aplikację w tym momencie, otrzymasz *HTTP 403 - Zabroniony* błąd: `Insufficient privileges to complete the operation`. Ten błąd występuje, ponieważ każde *uprawnienie tylko do aplikacji* wymaga zgody administratora: administrator globalny katalogu musi wyrazić zgodę na aplikację. Wybierz jedną z poniższych opcji w zależności od roli:

##### <a name="global-tenant-administrator"></a>Administrator globalny dzierżawy

> [!div renderon="docs"]
> Jeśli jesteś administratorem globalnym dzierżawy, przejdź do strony **Uprawnienia interfejsu API** w obszarze rejestracji aplikacji (w wersji zapoznawczej) witryny Azure Portal i wybierz pozycję **Wyraź zgodę administratora dla katalogu {nazwa dzierżawy}** ({nazwa dzierżawy} jest nazwą katalogu).

> [!div renderon="portal" class="sxs-lookup"]
> Jeśli jesteś administratorem globalnym, przejdź do strony **Uprawnienia interfejsu API** i wybierz pozycję **Wyraź zgodę administratora dla katalogu wpisz_tutaj_nazwę_dzierżawy**
> > [!div id="apipermissionspage"]
> > [Przejdź do strony Uprawnienia interfejsu API]()

##### <a name="standard-user"></a>Użytkownik standardowy

Jeśli jesteś standardowym użytkownikiem dzierżawy, musisz poprosić administratora globalnego o udzielenie zgody administratora dla aplikacji. Aby to zrobić, udostępnij administratorowi następujący adres URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Gdzie:
>> * `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4: Uruchom aplikację

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5. Uruchomienie aplikacji

Należy zainstalować zależności tego przykładu raz

```console
pip install -r requirements.txt
```

Następnie uruchom aplikację za pomocą wiersza polecenia lub konsoli:

```console
python confidential_client_secret_sample.py parameters.json
```

Powinieneś zobaczyć na danych wyjściowych konsoli niektóre fragment Json reprezentujący listę użytkowników w katalogu usługi Azure AD.

> [!IMPORTANT]
> Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst w plikach projektu, ze względów bezpieczeństwa zaleca się używanie certyfikatu zamiast klucza tajnego klienta, zanim będzie można uznać aplikację za produkcyjną. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [te instrukcje](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) w tym samym repozytorium GitHub dla tego przykładu, ale w drugim folderze **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Więcej informacji

### <a name="msal-python"></a>MĘTÓW MSAL

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) to biblioteka używana do logowania użytkowników i żądania tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Zgodnie z opisem ten przewodnik Szybki start żąda tokenów przy użyciu tożsamości własnej aplikacji zamiast uprawnień delegowanych. W tym przypadku przepływ uwierzytelniania jest określany jako *[przepływ OAuth poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md)*. Aby uzyskać więcej informacji na temat używania programu MSAL Python z aplikacjami demonów, zobacz [ten artykuł](scenario-daemon-overview.md).

 Program MSAL Python można zainstalować, uruchamiając następujące polecenie pip.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Aby dodać odwołanie do biblioteki MSAL, dodaj następujący kod:

```Python
import msal
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Gdzie: ||
> |---------|---------|
> | `config["secret"]` | Klucz tajny klienta utworzony dla aplikacji w witrynie Azure Portal. |
> | `config["client_id"]` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |
> | `config["authority"]`    | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. W chmurze publicznej jest to zwykle <https://login.microsoftonline.com/{tenant}>, gdzie {tenant} jest nazwą dzierżawy lub identyfikatorem dzierżawy.|

Więcej informacji można znaleźć w [dokumentacji dotyczącej metody `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Aby zażądać tokenu przy użyciu tożsamości aplikacji, należy użyć metody `AcquireTokenForClient`:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Gdzie:| |
> |---------|---------|
> | `config["scope"]` | Zawiera żądane zakresy. W przypadku klientów poufnych format powinien być podobny do `{Application ID URI}/.default`, aby wskazać, że żądane zakresy są zdefiniowane statycznie w obiekcie aplikacji ustawionym w witrynie Azure Portal (w przypadku programu Microsoft Graph element `{Application ID URI}` wskazuje na adres `https://graph.microsoft.com`). W przypadku niestandardowych `{Application ID URI}` interfejsów API sieci Web jest zdefiniowany w obszarze **Uwłaidanie** sekcji interfejsu API w rejestracji aplikacji usługi Azure Portal (w wersji zapoznawczej). |

Więcej informacji można znaleźć w [dokumentacji dotyczącej metody `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o aplikacjach demonów, zobacz stronę docelową scenariusza

> [!div class="nextstepaction"]
> [Aplikacja demona, która wywołuje internetowe interfejsy API](scenario-daemon-overview.md)

Aby zapoznać się z samouczkiem aplikacji demona, zobacz:

> [!div class="nextstepaction"]
> [Samouczek konsoli Daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon)

Dowiedz się więcej na temat uprawnień i wyrażania zgody:

> [!div class="nextstepaction"]
> [Uprawnienia i zgoda](v2-permissions-and-consent.md)

Aby dowiedzieć się więcej na temat przepływu autoryzacji w tym scenariuszu, zapoznaj się z przepływem poświadczeń klienta OAuth 2.0:

> [!div class="nextstepaction"]
> [Client credentials Oauth flow (Przepływ OAuth poświadczeń klienta)](v2-oauth2-client-creds-grant-flow.md)
