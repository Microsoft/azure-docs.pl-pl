---
title: Przewodnik dotyczący uwierzytelniania klientów usługi Azure sygnalizujących
description: Dowiedz się, jak wdrożyć własne uwierzytelnianie i zintegrować je z usługą Azure Signal Service, wykonując przykład E2E.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 03b112466ef094a578d47586a44ab383a5da1a9b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744888"
---
# <a name="azure-signalr-service-authentication"></a>Uwierzytelnianie w usłudze Azure SignalR Service

Ten samouczek opiera się na aplikacji pokoju rozmów wprowadzonej w przewodniku Szybki start. Jeśli jeszcze nie zostało wykonane ćwiczenie [Tworzenie pokoju rozmów za pomocą usługi SignalR Service](signalr-quickstart-dotnet-core.md), wykonaj je najpierw.

Z tego samouczka dowiesz się, jak zaimplementować własne uwierzytelnianie i zintegrować je z usługą Microsoft Azure SignalR Service.

Uwierzytelnianie początkowo używane w aplikacji pokoju rozmów przewodnika Szybki start jest zbyt proste dla realnych scenariuszy. Aplikacja umożliwia każdemu klientowi oświadczenie, kim jest, a serwer po prostu to zaakceptuje. To podejście nie nadaje się do rzeczywistych zastosowań, gdzie złośliwy użytkownik może podszyć się pod innych, aby uzyskać dostęp do poufnych danych.

Usługa [GitHub](https://github.com/) zapewnia interfejsy API uwierzytelniania bazujące na popularnym, będącym standardem branżowym protokole o nazwie [OAuth](https://oauth.net/). Te interfejsy API umożliwiają aplikacjom innych firm uwierzytelnianie kont usługi GitHub. W tym samouczku użyjesz tych interfejsów API do zaimplementowania uwierzytelniania za pomocą konta usługi GitHub przed zezwoleniem na logowanie klienta w aplikacji pokoju rozmów. Po uwierzytelnieniu konta usługi GitHub informacje o koncie zostaną dodane jako plik cookie używany przez klienta internetowego do uwierzytelniania.

Aby uzyskać więcej informacji na temat interfejsów API uwierzytelniania OAuth udostępnionych przez usługę GitHub, zobacz [Basics of Authentication (Podstawy uwierzytelniania)](https://developer.github.com/v3/guides/basics-of-authentication/).

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Doskonałym wyborem jest program [Visual Studio Code](https://code.visualstudio.com/), dostępny na platformach Windows, macOS i Linux.

Kod dla tego samouczka jest dostępny do pobrania w [repozytorium GitHub o nazwie AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![Gotowa aplikacja OAuth hostowana na platformie Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie nowej aplikacji OAuth za pomocą Twojego kona usługi GitHub
> * Dodawanie kontrolera uwierzytelniania w celu obsługi uwierzytelniania usługi GitHub
> * Wdrażanie aplikacji internetowej platformy ASP.NET Core na platformie Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są:

* Konto utworzone w usłudze [GitHub](https://github.com/)
* [Git](https://git-scm.com/)
* [Zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Skonfigurowana usługa Azure Cloud Shell](../cloud-shell/quickstart.md)
* Pobrane lub sklonowane repozytorium GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples)

## <a name="create-an-oauth-app"></a>Tworzenie aplikacji OAuth

1. Otwórz przeglądarkę internetową, przejdź do witryny `https://github.com` i zaloguj się na swoje konto.

2. W przypadku konta przejdź do pozycji **Ustawienia**  >  **Ustawienia dewelopera** i kliknij pozycję **zarejestruj nową aplikację** lub **nową aplikację OAuth** w obszarze *aplikacje uwierzytelniania OAuth* .

3. Użyj następujących ustawień dla nowej aplikacji OAuth, a następnie kliknij pozycję **Register application** (Zarejestruj aplikację):

    | Nazwa ustawienia | Sugerowana wartość | Opis |
    | ------------ | --------------- | ----------- |
    | Nazwa aplikacji | *Azure SignalR Chat* | Użytkownik usługi GitHub powinien mieć możliwość rozpoznania i zaufania aplikacji, za pomocą której następuje uwierzytelnienie.   |
    | Homepage URL (Adres URL strony głównej) | `http://localhost:5000/home` | |
    | Application description (Opis aplikacji) | *Przykład pokoju rozmów z użyciem usługi Azure SignalR Service z uwierzytelnianiem usługi GitHub* | Przydatny opis aplikacji, który pomoże użytkownikom Twojej aplikacji zrozumieć kontekst używanego uwierzytelniania. |
    | Authorization callback URL (Adres URL wywołania zwrotnego autoryzacji) | `http://localhost:5000/signin-github` | To ustawienie jest najważniejszym ustawieniem Twojej aplikacji OAuth. To jest adres URL wywołania zwrotnego, który usługa GitHub zwraca użytkownikowi po pomyślnym uwierzytelnieniu. W tym samouczku musisz użyć domyślnego adresu URL wywołania zwrotnego dla pakietu *AspNet.Security.OAuth.GitHub* , czyli */signin-github* .  |

4. Po zakończeniu rejestracji nowej aplikacji OAuth dodaj wartości *Client ID* i *Client Secret* do narzędzia Secret Manager za pomocą następujących poleceń. Zastąp wartości *Your_GitHub_Client_Id* i *Your_GitHub_Client_Secret* wartościami dla Twojej aplikacji OAuth.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>Implementowanie przepływu OAuth

### <a name="update-the-startup-class-to-support-github-authentication"></a>Aktualizacja klasy Startup w celu obsługi uwierzytelniania usługi GitHub

1. Dodaj odwołanie do najnowszych pakietów *Microsoft.AspNetCore.Authentication.Cookies* i *AspNet.Security.OAuth.GitHub* , a następnie przywróć wszystkie pakiety.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. Otwórz plik *Startup.cs* i dodaj instrukcje `using` dla następujących przestrzeni nazw:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. W górnej części klasy `Startup` dodaj stałe dla kluczy narzędzia Secret Manager przechowujących wpisy tajne aplikacji OAuth usługi GitHub.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Dodaj następujący kod do metody `ConfigureServices` w celu obsługi uwierzytelniania za pomocą aplikacji OAuth usługi GitHub:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Dodaj metodę pomocniczą `GetUserCompanyInfoAsync` do klasy `Startup`.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Zaktualizuj metodę `Configure` klasy Startup za pomocą następującego wiersza kodu i zapisz plik.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Dodawanie kontrolera uwierzytelniania

W tej sekcji zaimplementujesz interfejs API `Login`, który uwierzytelnia klientów przy użyciu aplikacji OAuth usługi GitHub. Po uwierzytelnieniu interfejs API doda plik cookie do odpowiedzi klienta internetowego przed przekierowaniem klienta z powrotem do aplikacji rozmów. Ten plik cookie będzie następnie służyć do identyfikacji klienta.

1. Dodaj nowy plik kodu kontrolera do katalogu *chattest\Controllers* . Nadaj plikowi nazwę *AuthController.cs* .

2. Dodaj następujący kod do kontrolera uwierzytelniania. Pamiętaj o aktualizacji przestrzeni nazw, jeśli Twoim katalogiem projektu nie był *chattest* :

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Zapisz zmiany.

### <a name="update-the-hub-class"></a>Aktualizacja klasy Hub

Domyślnie, gdy klient internetowy próbuje połączyć się z usługą SignalR Service, połączenie jest udzielane na podstawie tokenu dostępu, który jest dostarczany wewnętrznie. Ten token dostępu nie jest skojarzony z uwierzytelnioną tożsamością. Jest to tak naprawdę dostęp anonimowy.

W tej sekcji włączysz prawdziwe uwierzytelnianie, dodając atrybut `Authorize` do klasy Hub i aktualizując metody klasy Hub, aby odczytywać nazwę użytkownika z oświadczenia uwierzytelnionego użytkownika.

1. Otwórz plik *Hub\Chat.cs* i dodaj odwołania do tych przestrzeni nazw:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Zaktualizuj kod klasy Hub, jak pokazano poniżej. Ten kod dodaje atrybut `Authorize` do klasy `Chat` i używa tożsamości uwierzytelnionego użytkownika w metodach klasy Hub. Ponadto jest dodawana metoda `OnConnectedAsync`, która będzie rejestrować komunikaty systemowe do pokoju rozmów zawsze wtedy, gdy nowy klient nawiąże połączenie.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Zapisz zmiany.

### <a name="update-the-web-client-code"></a>Aktualizacja kodu klienta internetowego

1. Otwórz plik *wwwroot\index.html* i zastąp kod, który wyświetla monit o nazwę użytkownika, kodem, który używa pliku cookie zwróconego przez kontroler uwierzytelniania.

    Usuń poniższy kod z pliku *index.html* :

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Dodaj następujący kod zamiast powyższego kodu, aby użyć pliku cookie:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Tuż poniżej wiersza kodu dodanego w celu użycia pliku cookie dodaj następującą definicję funkcji `appendMessage`:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Zaktualizuj funkcje `bindConnectionMessage` i `onConnected` za pomocą następującego kodu, aby użyć funkcji `appendMessage`.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. W dolnej części pliku *index.html* zaktualizuj procedurę obsługi błędów dla metody `connection.start()` w przedstawiony poniżej sposób, aby monitować użytkownika o zalogowanie.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Zapisz zmiany.

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Zapisz zmiany we wszystkich plikach.

2. Skompiluj aplikację przy użyciu interfejsu wiersza polecenia platformy .NET Core, wykonując następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

3. Po pomyślnym zakończeniu kompilacji wykonaj następujące polecenie w celu lokalnego uruchomienia aplikacji internetowej:

    ```dotnetcli
    dotnet run
    ```

    Domyślnie aplikacja będzie hostowana lokalnie na porcie 5000:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. Uruchom okno przeglądarki i przejdź pod adres `http://localhost:5000`. Kliknij link **here** (tutaj) u góry, aby zalogować się za pomocą usługi GitHub.

    ![Gotowa aplikacja OAuth hostowana na platformie Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    Pojawi się monit o autoryzację dostępu aplikacji rozmów do Twojego konta usługi GitHub. Kliknij przycisk **Authorize** (Autoryzuj).

    ![Autoryzacja aplikacji OAuth](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    Nastąpi przekierowanie z powrotem do aplikacji rozmowy i zalogowanie przy użyciu Twojej nazwy konta usługi GitHub. Aplikacja internetowa ustaliła nazwę Twojego konta, uwierzytelniając Cię za pomocą nowego uwierzytelniania, które zostało przez Ciebie dodane.

    ![Konto zidentyfikowane](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Teraz, gdy aplikacja rozmów przeprowadza uwierzytelnianie za pomocą usługi GitHub i przechowuje informacje dotyczące uwierzytelniania jako pliki cookie, należy wdrożyć ją na platformie Azure, aby inni użytkownicy mogli się uwierzytelniać za pomocą swoich kont i komunikować się z innych stacji roboczych.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tej sekcji użyjesz interfejsu wiersza polecenia platformy Azure (CLI) z Azure Cloud Shell, aby utworzyć nową aplikację sieci Web w usłudze [Azure App Service](../app-service/index.yml) do hostowania aplikacji ASP.NET na platformie Azure. Aplikacja internetowa zostanie skonfigurowana do używania lokalnego wdrożenia usługi Git. Aplikacja internetowa będzie również skonfigurowana za pomocą parametrów połączenia usługi SignalR, wpisów tajnych aplikacji OAuth usługi GitHub i użytkownika wdrożenia.

Kroki opisane w tej sekcji używają rozszerzenia *signalr* dla interfejsu wiersza polecenia platformy Azure. Uruchom następujące polecenie, aby zainstalować rozszerzenie *signalr* dla interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az extension add -n signalr
```

Podczas tworzenia poniższych zasobów upewnij się, że jest używana ta sama grupa zasobów, w której znajdują się zasoby usługi SignalR Service. Takie podejście znacznie ułatwi późniejsze oczyszczanie, gdy zechcesz usunąć wszystkie zasoby. W podanych przykładach założono, że użyto nazwy grupy zalecanej w poprzednich samouczkach — *SignalRTestResources* .

### <a name="create-the-web-app-and-plan"></a>Tworzenie aplikacji internetowej i planu

Skopiuj poniższy tekst poleceń i zaktualizuj parametry. Wklej zaktualizowany skrypt do usługi Azure Cloud Shell, a następnie naciśnij klawisz **Enter** , aby utworzyć nowy plan usługi App Service i nową aplikację internetową.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parametr | Opis |
| -------------------- | --------------- |
| ResourceGroupName | Ta nazwa grupy zasobów została zasugerowana w poprzednich samouczkach. Warto zachować wszystkie zasoby samouczka zgrupowane razem. Użyj tej samej grupy zasobów co w poprzednich samouczkach. |
| WebAppPlan | Wprowadź nową, unikatową nazwę planu usługi App Service. |
| WebAppName | Będzie to nazwa nowej aplikacji internetowej i część adresu URL. Użyj unikatowej nazwy. Na przykład signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Dodawanie ustawień aplikacji do aplikacji internetowej

W tej sekcji dodasz ustawienia aplikacji dla następujących składników:

* Parametry połączenia zasobów usługi SignalR Service
* Identyfikator klienta aplikacji OAuth usługi GitHub
* Wpis tajny klienta aplikacji OAuth usługi GitHub

Skopiuj poniższy tekst poleceń i zaktualizuj parametry. Wklej zaktualizowany skrypt do usługi Azure Cloud Shell, a następnie naciśnij klawisz **Enter** , aby dodać ustawienia aplikacji:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parametr | Opis |
| -------------------- | --------------- |
| GitHubClientId | Przypisz tej zmiennej tajny identyfikator klienta Twojej aplikacji OAuth usługi GitHub. |
| GitHubClientSecret | Przypisz tej zmiennej tajne hasło Twojej aplikacji OAuth usługi GitHub. |
| ResourceGroupName | Zaktualizuj tę zmienną, aby miała tę samą nazwę grupy zasobów, która została użyta w poprzedniej sekcji. |
| SignalRServiceResource | Zaktualizuj tę zmienną za pomocą nazwy zasobu usługi SignalR Service, który został utworzony w poradniku Szybki start. Na przykład signalrtestsvc48778624. |
| WebAppName | Zaktualizuj tę zmienną za pomocą nazwy nowej aplikacji internetowej, która została utworzona w poprzedniej sekcji. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Konfigurowanie aplikacji internetowej na potrzeby lokalnego wdrożenia usługi Git

W usłudze Azure Cloud Shell wklej poniższy skrypt. Ten skrypt tworzy nową nazwę i hasło użytkownika wdrożenia, których użyjesz podczas wdrażania Twojego kodu w aplikacji internetowej za pomocą usługi Git. Skrypt skonfiguruje również aplikację internetową do wdrożenia za pomocą lokalnego repozytorium Git i zwróci adres URL wdrożenia Git.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parametr | Opis |
| -------------------- | --------------- |
| DeploymentUserName | Wybierz nową nazwę użytkownika wdrożenia. |
| DeploymentUserPassword | Wybierz hasło dla nowego użytkownika wdrożenia. |
| ResourceGroupName | Użyj tej samej nazwy grupy zasobów, która została użyta w poprzedniej sekcji. |
| WebAppName | Będzie to nazwa nowej, wcześniej utworzonej aplikacji internetowej. |

Zanotuj adres URL wdrożenia Git zwracany przez to polecenie. Tego adresu URL użyjesz później.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Wdrażanie kodu w aplikacji internetowej platformy Azure

Aby wdrożyć swój kod, należy wykonać następujące polecenia w powłoce Git.

1. Przejdź do katalogu głównego katalogu projektu. Jeśli nie masz projektu zainicjowanego za pomocą repozytorium Git, wykonaj następujące polecenie:

    ```bash
    git init
    ```

2. Dodaj zdalne repozytorium dla zanotowanego wcześniej adresu URL wdrożenia Git:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Umieść wszystkie pliki w lokalizacji przejściowej w zainicjowanym repozytorium, a następnie dodaj zatwierdzenie.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Wdróż swój kod w aplikacji internetowej na platformie Azure.

    ```bash
    git push Azure master
    ```

    Pojawi się monit o uwierzytelnienie w celu wdrożenia kodu na platformie Azure. Wprowadź nazwę i hasło użytkownika wdrożenia utworzone powyżej.

### <a name="update-the-github-oauth-app"></a>Aktualizowanie aplikacji OAuth usługi GitHub

Ostatnim zadaniem, które należy wykonać, jest aktualizacja **adresu URL strony głównej** i **adresu URL wywołania zwrotnego autoryzacji** aplikacji OAuth usługi GitHub, aby wskazywał nową hostowaną aplikację.

1. Otwórz [https://github.com](https://github.com) w przeglądarce i przejdź do **ustawień** ustawienia  >  **dewelopera**  >  **aplikacje OAuth** .

2. Kliknij aplikację uwierzytelniania oraz zaktualizuj **adres URL strony głównej** i **adres URL wywołania zwrotnego autoryzacji** w sposób przedstawiony poniżej:

    | Ustawienie | Przykład |
    | ------- | ------- |
    | Homepage URL (Adres URL strony głównej) | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | Authorization callback URL (Adres URL wywołania zwrotnego autoryzacji) | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Przejdź do adresu URL swojej aplikacji internetowej i przetestuj aplikację.

    ![Gotowa aplikacja OAuth hostowana na platformie Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz przejść do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie w następnym samouczku.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów** .

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *SignalRTestResources* . Dla grupy zasobów na liście wyników kliknij pozycję **...** , a następnie kliknij pozycję **Usuń grupę zasobów** .

![Usuń](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń** .

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostało dodane uwierzytelnianie za pomocą protokołu OAuth w celu zapewnienia lepszego podejścia do uwierzytelniania za pomocą usługi Azure SignalR Service. Aby dowiedzieć się więcej o korzystaniu z serwera Azure SignalR Server, przejdź do przykładów interfejsu wiersza polecenia platformy Azure dla usługi SignalR Service.

> [!div class="nextstepaction"]
> [Przykłady Interfejsu wiersza polecenia usługi Azure SignalR](./signalr-reference-cli.md)
