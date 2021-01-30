---
title: 'Szybki Start: wywoływanie Microsoft Graph z demona języka Java | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak aplikacja Java może uzyskać token dostępu i wywołać interfejs API chroniony przez punkt końcowy platformy tożsamości firmy Microsoft przy użyciu tożsamości aplikacji
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 9c6571793d2317097574d0afdc7137b3a3d5ad6d
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064528"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji konsolowej Java przy użyciu tożsamości aplikacji

W tym przewodniku szybki start pobierasz i uruchamiasz przykładowy kod, który pokazuje, jak aplikacja Java może uzyskać token dostępu przy użyciu tożsamości aplikacji w celu wywołania interfejsu API Microsoft Graph i wyświetlenia [listy użytkowników](/graph/api/user-list) w katalogu. Przykład kodu demonstruje, jak zadanie nienadzorowane lub usługa systemu Windows mogą być uruchamiane przy użyciu tożsamości aplikacji, a nie tożsamości użytkownika. 

> [!div renderon="docs"]
> ![Pokazuje sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego przykładu potrzebne są:

- [Zestaw Java Development Kit (JDK)](https://openjdk.java.net/) 8 lub nowszy
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start

> [!div renderon="docs" class="sxs-lookup"]
>
> Dostępne są dwie opcje uruchomienia aplikacji szybkiego startu: Express (opcja 1 poniżej) i ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do nowego okienka [Azure Portal-rejestracje aplikacji](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs) .
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację za pomocą tylko jednego kliknięcia.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> Aby ręcznie zarejestrować aplikację i dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do strony Microsoft Identity Platform for Developers [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Gdy zostanie wyświetlona strona **zarejestruj aplikację** , wprowadź informacje rejestracyjne swojej aplikacji.
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

#### <a name="step-2-download-your-java-project"></a>Krok 2. Pobieranie projektu Java

> [!div renderon="docs"]
> [Pobierz projekt demona języka Java](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-java-project"></a>Krok 3. Konfigurowanie projektu Java
>
> 1. Wyodrębnij plik zip do folderu lokalnego blisko folderu głównego dysku, na przykład **C:\Azure-Samples**.
> 1. Przejdź do folderu Sub **msal-Client-Credential-Secret**.
> 1. Edytuj **src\main\resources\application.Properties** i Zastąp wartości pól, `AUTHORITY` `CLIENT_ID` i `SECRET` z następującym fragmentem kodu:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Gdzie:
>    - `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.
>    - `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here` — zastąp tę wartość kluczem tajnym klienta utworzonym w kroku 1.
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)**, **Identyfikator katalogu (dzierżawy)**, przejdź do strony **Przegląd** aplikacji w witrynie Azure Portal. Aby wygenerować nowy klucz, przejdź do strony **Certyfikaty i klucze tajne**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Krok 3. zgoda administratora

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Krok 4. Zgoda administratora

Jeśli spróbujesz uruchomić aplikację w tym momencie, otrzymasz komunikat o błędzie *HTTP 403 — Dostęp zabroniony* : `Insufficient privileges to complete the operation` . Ten błąd występuje, ponieważ każde *uprawnienie tylko do aplikacji* wymaga zgody administratora: Administrator globalny katalogu musi wyrazić zgodę na swoją aplikację. Wybierz jedną z poniższych opcji, w zależności od roli:

##### <a name="global-tenant-administrator"></a>Administrator globalny dzierżawy

> [!div renderon="docs"]
> Jeśli jesteś administratorem globalnym dzierżawy, przejdź do strony **Uprawnienia interfejsu API** w obszarze rejestracji aplikacji (w wersji zapoznawczej) witryny Azure Portal i wybierz pozycję **Wyraź zgodę administratora dla katalogu {nazwa dzierżawy}** ({nazwa dzierżawy} jest nazwą katalogu).

> [!div renderon="portal" class="sxs-lookup"]
> Jeśli jesteś administratorem globalnym, przejdź do strony **Uprawnienia interfejsu API** i wybierz pozycję **Wyraź zgodę administratora dla katalogu wpisz_tutaj_nazwę_dzierżawy**
> > [!div id="apipermissionspage"]
> > [Przejdź do strony Uprawnienia interfejsu API]()

##### <a name="standard-user"></a>Użytkownik standardowy

Jeśli jesteś użytkownikiem standardowym Twojej dzierżawy, musisz poprosił administratora globalnego o przyznanie zgody administratora dla aplikacji. Aby to zrobić, udostępnij administratorowi następujący adres URL:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Gdzie:
>> * `Enter_the_Tenant_Id_Here` — zastąp tę wartość wartością **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` jest **identyfikatorem aplikacji (klienta)** dla zarejestrowanej aplikacji.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Krok 4. Uruchamianie aplikacji

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Krok 5. Uruchomienie aplikacji

Możesz przetestować próbkę bezpośrednio przez uruchomienie głównej metody ClientCredentialGrant. Java z poziomu środowiska IDE.

Z powłoki lub wiersza polecenia:

```
$ mvn clean compile assembly:single
```

Spowoduje to wygenerowanie pliku msal-Client-Credential-Secret-1.0.0. jar w katalogu/targets. Uruchom to przy użyciu pliku wykonywalnego języka Java, takiego jak poniżej:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

Po uruchomieniu aplikacja powinna wyświetlić listę użytkowników w skonfigurowanej dzierżawie.


> [!IMPORTANT]
> Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst w plikach projektu, ze względów bezpieczeństwa zaleca się używanie certyfikatu zamiast klucza tajnego klienta, zanim będzie można uznać aplikację za produkcyjną. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [te instrukcje](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) w tym samym repozytorium GitHub dla tego przykładu, ale w drugim folderze **msal-Client-Credential-Certificate**

## <a name="more-information"></a>Więcej informacji

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) to biblioteka służąca do logowania użytkowników i żądania tokenów używanych w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft. Zgodnie z opisem, ten przewodnik Szybki Start żąda tokenów przy użyciu tożsamości własnej aplikacji zamiast uprawnień delegowanych. W tym przypadku przepływ uwierzytelniania jest określany jako *[przepływ OAuth poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md)*. Aby uzyskać więcej informacji na temat używania języka MSAL Java z aplikacjami demonów, zobacz [ten artykuł](scenario-daemon-overview.md).

Dodaj MSAL4J do aplikacji za pomocą Maven lub Gradle, aby zarządzać zależnościami, wprowadzając następujące zmiany w pliku pom.xml (Maven) lub kompilacji. Gradle (Gradle) aplikacji.

W pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

W Build. Gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Dodaj odwołanie do MSAL for Java, dodając następujący kod na początku pliku, w którym będziesz używać MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

Następnie zainicjuj bibliotekę MSAL, używając następującego kodu:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Gdzie: |Opis |
> |---------|---------|
> | `CLIENT_SECRET` | Klucz tajny klienta utworzony dla aplikacji w witrynie Azure Portal. |
> | `CLIENT_ID` | Jest **identyfikatorem aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. Tę wartość można znaleźć na stronie **Przegląd** aplikacji w witrynie Azure Portal. |
> | `AUTHORITY`    | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. W chmurze publicznej jest to zwykle `https://login.microsoftonline.com/{tenant}`, gdzie {tenant} jest nazwą dzierżawy lub identyfikatorem dzierżawy.|

### <a name="requesting-tokens"></a>Przesyłanie żądań tokenów

Aby zażądać tokenu przy użyciu tożsamości aplikacji, należy użyć metody `acquireToken`:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Gdzie:| Opis |
> |---------|---------|
> | `SCOPE` | Zawiera żądane zakresy. W przypadku klientów poufnych format powinien być podobny do `{Application ID URI}/.default`, aby wskazać, że żądane zakresy są zdefiniowane statycznie w obiekcie aplikacji ustawionym w witrynie Azure Portal (w przypadku programu Microsoft Graph element `{Application ID URI}` wskazuje na adres `https://graph.microsoft.com`). W przypadku niestandardowych interfejsów API sieci Web `{Application ID URI}` jest zdefiniowany w sekcji **UWIDACZNIANIE interfejsu API** w rejestracji aplikacji w witrynie Azure Portal (wersja zapoznawcza). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat aplikacji demonów, zobacz stronę docelową scenariusza

> [!div class="nextstepaction"]
> [Aplikacja demona, która wywołuje interfejsy API sieci Web](scenario-daemon-overview.md)
