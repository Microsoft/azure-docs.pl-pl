---
title: Przewodnik Szybki start pokazujący, jak korzystać z usługi Azure SignalR Service
description: Przewodnik Szybki start pokazujący, jak za pomocą usługi Azure SignalR Service utworzyć pokój rozmów przy użyciu aplikacji ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: b5a2064e2fd80b895b0e801090c66d7119cf69dd
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151014"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Szybki Start: Tworzenie pokoju rozmów przy użyciu usługi sygnalizującej

Usługa Azure SignalR Service to usługa platformy Azure, która ułatwia deweloperom tworzenie aplikacji internetowych z funkcjami działającymi w czasie rzeczywistym. Ta usługa była pierwotnie oparta na [sygnale dla ASP.NET Core 2,1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), ale teraz obsługuje nowsze wersje.

W tym artykule pokazano, jak rozpocząć pracę z usługą Azure SignalR Service. W tym przewodniku szybki start utworzysz aplikację czatu przy użyciu aplikacji sieci Web ASP.NET Core MVC. Ta aplikacja nawiąże połączenie z zasobem usługi Azure SignalR Service, aby umożliwić aktualizacje zawartości w czasie rzeczywistym. Będziesz hostować aplikację sieci Web lokalnie i łączyć się z wieloma klientami przeglądarki. Każdy klient będzie mógł wypychać aktualizacje zawartości do innych klientów. 

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Jedną z opcji jest [Visual Studio Code](https://code.visualstudio.com/), która jest dostępna na platformach Windows, MacOS i Linux.

Kod dla tego samouczka jest dostępny do pobrania w [repozytorium GitHub o nazwie AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Możesz również utworzyć zasoby platformy Azure używane w tym przewodniku Szybki Start, wykonując następujące czynności: [Utwórz skrypt usługi sygnalizującej](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Pobrane lub sklonowane repozytorium GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) 

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="create-an-azure-signalr-resource"></a>Tworzenie zasobu usługi Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

W tej sekcji użyjesz [interfejsu wiersza polecenia platformy .NET Core](/dotnet/core/tools/) do utworzenia projektu aplikacji sieci Web ASP.NET Core MVC. Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core przez program Visual Studio jest to, że jest on dostępny na platformach Windows, macOS i Linux. 

1. Utwórz folder dla projektu. Ten przewodnik Szybki Start używa folderu *E:\Testing\chattest* .

2. W nowym folderze Uruchom następujące polecenie, aby utworzyć projekt:

    ```dotnetcli
    dotnet new mvc
    ```

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="add-secret-manager-to-the-project"></a>Dodawanie narzędzia Secret Manager do projektu

W tej sekcji dodasz do projektu [Narzędzie do zarządzania kluczami tajnymi](/aspnet/core/security/app-secrets) . Narzędzie Secret Manager przechowuje dane poufne do pracy programistycznej poza drzewem projektu. Takie podejście pomaga uniknąć przypadkowego udostępniania wpisów tajnych aplikacji w kodzie źródłowym.

1. Otwórz plik *csproj*. Dodaj element `DotNetCliToolReference`, aby uwzględnić narzędzia *Microsoft.Extensions.SecretManager.Tools*. Dodaj również `UserSecretsId` element, jak pokazano w poniższym kodzie dla *csproj*i Zapisz plik.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="add-azure-signalr-to-the-web-app"></a>Dodawanie usługi Azure SignalR do aplikacji internetowej

1. Dodaj odwołanie do `Microsoft.Azure.SignalR` pakietu NuGet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Uruchom następujące polecenie, aby przywrócić pakiety dla projektu:

    ```dotnetcli
    dotnet restore
    ```

3. Dodaj wpis tajny o nazwie *Azure:SignalR:ConnectionString* do narzędzia Secret Manager. 

    Ten wpis tajny będzie zawierać parametry połączenia umożliwiające dostęp do zasobu usługi SignalR Service. *Azure: signaler: ConnectionString* jest domyślnym kluczem konfiguracji, który sygnalizuje wyszukiwanie, aby nawiązać połączenie. Zastąp wartość w następującym poleceniu parametrami połączenia dla zasobu usługi sygnalizującego.

    To polecenie należy uruchomić w tym samym katalogu, w którym znajduje się plik *. csproj* .

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Menedżer wpisów tajnych będzie używany tylko w celu testowania aplikacji sieci Web, gdy jest ona hostowana lokalnie. W kolejnym samouczku zostanie wdrożona aplikacja internetowa czatu na platformie Azure. Po wdrożeniu aplikacji sieci Web na platformie Azure użyjesz ustawienia aplikacji zamiast przechowywania parametrów połączenia za pomocą Menedżera wpisów tajnych.

    Ten klucz tajny jest dostępny z interfejsem API konfiguracji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Otwórz *Startup.cs* i zaktualizuj `ConfigureServices` metodę, aby użyć usługi Azure Signaling, wywołując `AddSignalR()` metody i `AddAzureSignalR()` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Nie przekazując parametru do `AddAzureSignalR()` , ten kod używa domyślnego klucza konfiguracji dla parametrów połączenia zasobu usługi sygnalizującego. Domyślnym kluczem konfiguracji jest *Azure: sygnalizującer: ConnectionString*.

5. W *Startup.cs*zaktualizuj metodę, `Configure` zastępując ją poniższym kodem.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Dodawanie klasy centrum

W przypadku centrum jest głównym składnikiem, który uwidacznia zestaw metod, które mogą być wywoływane z klienta programu. W tej sekcji klasa centrum jest definiowania za pomocą dwóch metod:

* `Broadcast`: ta metoda rozgłasza komunikat do wszystkich klientów.
* `Echo`: ta metoda wysyła komunikat z powrotem do elementu wywołującego.

Obie metody używają `Clients` interfejsu, który udostępnia zestaw SDK ASP.NET Core. Ten interfejs zapewnia dostęp do wszystkich połączonych klientów, dzięki czemu można wypchnąć zawartość do klientów.

1. W katalogu projektu dodaj nowy folder o nazwie *Hub*. Dodaj nowy plik z kodem centrum o nazwie *ChatHub.cs* do nowego folderu.

2. Dodaj następujący kod do *ChatHub.cs* , aby zdefiniować klasę Hub i zapisać plik.

    Zaktualizuj przestrzeń nazw dla tej klasy, jeśli użyto nazwy projektu, która różni się od *sygnalizującer. MVC*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Dodawanie interfejsu klienta dla aplikacji sieci Web

Interfejs użytkownika klienta dla tej aplikacji pokoju rozmów będzie zawierać kod HTML i JavaScript w pliku o nazwie *index.html* w katalogu *wwwroot* .

Skopiuj plik *CSS/site. css* z folderu *wwwroot* [repozytorium Samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Zastąp arkusz CSS */site. css* projektu skopiowanego.

Oto kod główny *index.html*:

Utwórz nowy plik w katalogu *wwwroot* o nazwie *index.html*, skopiuj i wklej następujący kod HTML do nowo utworzonego pliku:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

Kod w *index.html* wywołań, `HubConnectionBuilder.build()` Aby NAwiązać połączenie HTTP z zasobem usługi Azure Signal.

Jeśli połączenie zostanie nawiązane pomyślnie, zostanie przekazane do metody `bindConnectionMessage`, która dodaje procedury obsługi zdarzeń dla przychodzących wypchnięć zawartości do klienta. 

Metoda `HubConnection.start()` uruchamia komunikację z centrum. Następnie program `onConnected()` dodaje programy obsługi zdarzeń przycisku. Te procedury obsługi używają połączenia w celu umożliwienia temu klientowi wpychania aktualizacji zawartości do wszystkich połączonych klientów.

## <a name="add-a-development-runtime-profile"></a>Dodawanie profilu środowiska uruchomieniowego programowania

W tej sekcji dodasz środowisko środowiska uruchomieniowego dla ASP.NET Core. Aby uzyskać więcej informacji, zobacz [Working with Multiple Environments in ASP.NET Core](/aspnet/core/fundamentals/environments).

1. Utwórz folder o nazwie *Właściwości* w projekcie.

2. Dodaj nowy plik o nazwie *launchSettings.js* do folderu z następującą zawartością i Zapisz plik.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

1. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```dotnetcli
    dotnet run
    ```

    Aplikacja będzie hostowana lokalnie na porcie 5000, zgodnie z konfiguracją w naszym profilu środowiska uruchomieniowego programu Development:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Otwórz dwa okna przeglądarki. W każdej przeglądarce przejdź do `http://localhost:5000` . Zostanie wyświetlony monit o wprowadzenie nazwy. Wprowadź nazwę klienta dla obu klientów i przetestuj zawartość wiadomości między obu klientów za pomocą przycisku **Wyślij** .

    ![Przykład rozmowy z grupą sygnałów platformy Azure](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przejdziesz do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki Start i użyć ich ponownie.

Po zakończeniu korzystania z przykładowej aplikacji szybkiego startu możesz usunąć zasoby platformy Azure utworzone w tym przewodniku Szybki Start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usuwanie grupy zasobów jest nieodwracalne i obejmuje wszystkie zasoby w tej grupie. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli utworzono zasoby do hostowania tego przykładu w istniejącej grupie zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy z nich osobno, zamiast usuwać grupę zasobów.

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. W grupie zasobów na liście wynik wybierz wielokropek (**...**) > **Usuń grupę zasobów**.

![Wybory dotyczące usuwania grupy zasobów](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie jej zasoby zostaną usunięte.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy zasób usługi Azure Signal Service. Następnie został on użyty z aplikacją sieci Web ASP.NET Core do wypychania aktualizacji zawartości w czasie rzeczywistym do wielu podłączonych klientów. Aby dowiedzieć się więcej o korzystaniu z usługi Azure Signal Service, przejdź do samouczka, który pokazuje uwierzytelnianie.

> [!div class="nextstepaction"]
> [Uwierzytelnianie w usłudze Azure SignalR Service](./signalr-concept-authenticate-oauth.md)

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsnetcore)