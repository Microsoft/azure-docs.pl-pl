---
title: Przewodnik Szybki start pokazujący, jak korzystać z usługi Azure SignalR Service
description: Przewodnik Szybki start pokazujący, jak za pomocą usługi Azure SignalR Service utworzyć pokój rozmów przy użyciu aplikacji ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: c3a482dc1fa63b997b948cc3bc5cacefc9c2ad0a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928534"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Szybki Start: Tworzenie pokoju rozmów przy użyciu usługi sygnalizującej


Usługa Azure SignalR Service to usługa platformy Azure, która ułatwia deweloperom tworzenie aplikacji internetowych z funkcjami działającymi w czasie rzeczywistym. Ta usługa jest oparta na usłudze [sygnalizującej dla ASP.NET Core 2,1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1), ale również obsługuje program [sygnalizujący ASP.NET Core 3,0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

W tym artykule pokazano, jak rozpocząć pracę z usługą Azure SignalR Service. W tym przewodniku szybki start utworzysz aplikację czatu przy użyciu aplikacji sieci Web ASP.NET Core MVC. Ta aplikacja nawiąże połączenie z zasobem usługi Azure SignalR Service, aby umożliwić aktualizacje zawartości w czasie rzeczywistym. Będziesz hostować aplikację sieci Web lokalnie i łączyć się z wieloma klientami przeglądarki. Każdy klient będzie mógł wypychać aktualizacje zawartości do innych klientów. 

Do wykonania kroków tego przewodnika Szybki start możesz użyć dowolnego edytora kodu. Jedną z opcji jest [Visual Studio Code](https://code.visualstudio.com/), która jest dostępna na platformach Windows, MacOS i Linux.

Kod dla tego samouczka jest dostępny do pobrania w [repozytorium GitHub o nazwie AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Możesz również utworzyć zasoby platformy Azure używane w tym przewodniku Szybki Start, wykonując następujące czynności: [Utwórz skrypt usługi sygnalizującej](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [zestaw .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Pobrane lub sklonowane repozytorium GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) 

## <a name="create-an-azure-signalr-resource"></a>Tworzenie zasobu usługi Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

W tej sekcji użyjesz [interfejsu wiersza polecenia platformy .NET Core](https://docs.microsoft.com/dotnet/core/tools/) do utworzenia projektu aplikacji sieci Web ASP.NET Core MVC. Zaletą korzystania z interfejs wiersza polecenia platformy .NET Core przez program Visual Studio jest to, że jest on dostępny na platformach Windows, macOS i Linux. 

1. Utwórz folder dla projektu. Ten przewodnik Szybki Start używa folderu *E:\Testing\chattest* .

2. W nowym folderze Uruchom następujące polecenie, aby utworzyć projekt:

    ```dotnetcli
    dotnet new mvc
    ```


## <a name="add-secret-manager-to-the-project"></a>Dodawanie narzędzia Secret Manager do projektu

W tej sekcji dodasz do projektu [Narzędzie do zarządzania kluczami tajnymi](https://docs.microsoft.com/aspnet/core/security/app-secrets) . Narzędzie Secret Manager przechowuje dane poufne do pracy programistycznej poza drzewem projektu. Takie podejście pomaga uniknąć przypadkowego udostępniania wpisów tajnych aplikacji w kodzie źródłowym.

1. Otwórz plik *csproj*. Dodaj element `DotNetCliToolReference`, aby uwzględnić narzędzia *Microsoft.Extensions.SecretManager.Tools*. Dodaj również `UserSecretsId` element, jak pokazano w poniższym kodzie dla *csproj*i Zapisz plik.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

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

    Ten klucz tajny jest dostępny z interfejsem API konfiguracji. Dwukropek (:) działa w nazwie konfiguracji z interfejsem API konfiguracji na wszystkich obsługiwanych platformach. Zobacz [Konfiguracja według środowiska](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Otwórz plik *Startup.cs* i zaktualizuj metodę `ConfigureServices` tak, aby używała usługi SignalR Service przez wywołanie metody `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Nie przekazując parametru do `AddAzureSignalR()` , ten kod używa domyślnego klucza konfiguracji dla parametrów połączenia zasobu usługi sygnalizującego. Domyślnym kluczem konfiguracji jest *Azure: sygnalizującer: ConnectionString*.

5. Również w *Startup.cs*należy zaktualizować `Configure` metodę przez zastąpienie wywołania do `app.UseStaticFiles()` następującego kodu i zapisanie pliku tylko dla ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Dla ASP.NET Core 3 + Zastąp powyższy kod:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Dodawanie klasy centrum

W przypadku centrum jest głównym składnikiem, który uwidacznia zestaw metod, które mogą być wywoływane z klienta programu. W tej sekcji klasa centrum jest definiowania za pomocą dwóch metod: 

* `Broadcast`: ta metoda rozgłasza komunikat do wszystkich klientów.
* `Echo`: ta metoda wysyła komunikat z powrotem do elementu wywołującego.

Obie metody używają `Clients` interfejsu, który udostępnia zestaw SDK ASP.NET Core. Ten interfejs zapewnia dostęp do wszystkich połączonych klientów, dzięki czemu można wypchnąć zawartość do klientów.

1. W katalogu projektu dodaj nowy folder o nazwie *Hub*. Dodaj nowy plik kodu centrum o nazwie *Chat.cs* do nowego folderu.

2. Dodaj następujący kod do *Chat.cs* , aby zdefiniować klasę Hub i zapisać plik. 

    Zaktualizuj przestrzeń nazw dla tej klasy, jeśli została użyta nazwa projektu inna niż *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Dodawanie interfejsu klienta dla aplikacji sieci Web

Interfejs użytkownika klienta dla tej aplikacji pokoju rozmów będzie zawierać kod HTML i JavaScript w pliku o nazwie *index.html* w katalogu *wwwroot* .

Skopiuj plik *index.html* , folder *CSS* i folder *scripts* z folderu *wwwroot* [repozytorium Samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Wklej je do folderu *wwwroot* Twojego projektu.

Oto kod główny *index.html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

Kod w *index.html* wywołań, `HubConnectionBuilder.build()` Aby NAwiązać połączenie HTTP z zasobem usługi Azure Signal.

Jeśli połączenie zostanie nawiązane pomyślnie, zostanie przekazane do metody `bindConnectionMessage`, która dodaje procedury obsługi zdarzeń dla przychodzących wypchnięć zawartości do klienta. 

Metoda `HubConnection.start()` uruchamia komunikację z centrum. Następnie program `onConnected()` dodaje programy obsługi zdarzeń przycisku. Te procedury obsługi używają połączenia w celu umożliwienia temu klientowi wpychania aktualizacji zawartości do wszystkich połączonych klientów.

## <a name="add-a-development-runtime-profile"></a>Dodawanie profilu środowiska uruchomieniowego programowania

W tej sekcji dodasz środowisko środowiska uruchomieniowego dla ASP.NET Core. Aby uzyskać więcej informacji, zobacz [Working with Multiple Environments in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Utwórz folder o nazwie *Właściwości* w projekcie.

2. Dodaj nowy plik o nazwie *launchSettings.js* do folderu z następującą zawartością i Zapisz plik.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

1. Aby skompilować aplikację przy użyciu interfejs wiersza polecenia platformy .NET Core, uruchom następujące polecenie w powłoce poleceń:

    ```dotnetcli
    dotnet build
    ```

2. Po pomyślnym zakończeniu kompilacji Uruchom następujące polecenie, aby uruchomić aplikację sieci Web lokalnie:

    ```dotnetcli
    dotnet run
    ```

    Aplikacja będzie hostowana lokalnie na porcie 5000, zgodnie z konfiguracją w naszym profilu środowiska uruchomieniowego programu Development:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Development
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.    
    ```

3. Otwórz dwa okna przeglądarki. W każdej przeglądarce przejdź do `http://localhost:5000` . Zostanie wyświetlony monit o wprowadzenie nazwy. Wprowadź nazwę klienta dla obu klientów i przetestuj zawartość wiadomości między obu klientów za pomocą przycisku **Wyślij** .

    ![Przykład rozmowy z grupą sygnałów platformy Azure](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przejdziesz do kolejnego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki Start i użyć ich ponownie.

Po zakończeniu korzystania z przykładowej aplikacji szybkiego startu możesz usunąć zasoby platformy Azure utworzone w tym przewodniku Szybki Start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usuwanie grupy zasobów jest nieodwracalne i obejmuje wszystkie zasoby w tej grupie. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli utworzono zasoby do hostowania tego przykładu w istniejącej grupie zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy z nich osobno, zamiast usuwać grupę zasobów.
> 
> 

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy** wpisz nazwę grupy zasobów. W instrukcjach dla tego przewodnika Szybki start używana była grupa zasobów o nazwie *SignalRTestResources*. W grupie zasobów na liście wynik wybierz wielokropek (**...**) > **Usuń grupę zasobów**.

   
![Wybory dotyczące usuwania grupy zasobów](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.
   
Po krótkim czasie grupa zasobów i wszystkie jej zasoby zostaną usunięte.



## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono nowy zasób usługi Azure Signal Service. Następnie został on użyty z aplikacją sieci Web ASP.NET Core do wypychania aktualizacji zawartości w czasie rzeczywistym do wielu podłączonych klientów. Aby dowiedzieć się więcej o korzystaniu z usługi Azure Signal Service, przejdź do samouczka, który pokazuje uwierzytelnianie.

> [!div class="nextstepaction"]
> [Uwierzytelnianie w usłudze Azure SignalR Service](./signalr-concept-authenticate-oauth.md)


