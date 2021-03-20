---
title: 'Samouczek: Tworzenie aplikacji czatu na serwerze Blazor — usługa Azure Signal'
description: W ramach tego samouczka nauczysz się tworzyć i modyfikować aplikację serwera Blazor za pomocą usługi Azure Signal Service
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: b0059e986b9a6ba8152a1a61f8d696f1caa4646a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97755909"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Samouczek: Tworzenie aplikacji czatu serwera Blazor

W tym samouczku pokazano, jak utworzyć i zmodyfikować aplikację serwera Blazor. Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie prostego pokoju rozmów przy użyciu aplikacji Blazor Server.
> * Zmodyfikuj składniki Razor.
> * Użyj obsługi zdarzeń i powiązania danych w składnikach.
> * Szybkie wdrażanie do Azure App Service w programie Visual Studio.
> * Przeprowadź migrację lokalnego sygnału do usługi Azure Signal Service.

## <a name="prerequisites"></a>Wymagania wstępne
* Zainstaluj [zestaw SDK platformy .NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0) (wersja >= 3.0.100)
* Zainstaluj [program Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (wersja >= 16,3)
> Wersja zapoznawcza programu Visual Studio 2019 jest również dostępna przy użyciu najnowszego szablonu aplikacji Blazor Server, który ma nowszą wersję .Net Core.

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Tworzenie lokalnego pokoju rozmów w aplikacji serwera Blazor

W programie Visual Studio 2019 w wersji 16.2.0 usługa Azure Signal Service to kompilacja oparta na procesie publikowania aplikacji sieci Web, a zarządzanie zależnościami między usługą Web App i sygnalizującą będzie znacznie wygodniejsze. Możesz pracować na lokalnym sygnale w środowisku deweloperskim i pracować z usługą Azure Signal Service dla Azure App Service w tym samym czasie bez wprowadzania żadnych zmian w kodzie.

1. Tworzenie aplikacji czatu Blazor
   
   W programie Visual Studio wybierz kolejno pozycje Utwórz nowy projekt — > Blazor App — > (Nadaj aplikacji nazwę i wybierz folder) — > Blazor aplikacji serwerowej. Upewnij się, że już zainstalowano program zestaw .NET Core SDK 3.0 +, aby umożliwić programowi Visual Studio poprawne rozpoznanie platformy docelowej.

   [![W obszarze Utwórz nowy projekt są wybrane szablony aplikacji Blazor. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Lub uruchom polecenie cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Dodaj `BlazorChatSampleHub.cs` plik do zaimplementowania `Hub` na potrzeby rozmowy.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Dodaj punkt końcowy centrum w programie `Startup.Configure()` .
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Zainstaluj `Microsoft.AspNetCore.SignalR.Client` pakiet, aby użyć klienta sygnalizującego.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Utwórz `ChatRoom.razor` w obszarze `Pages` folder, aby zaimplementować klienta sygnalizującego. Wykonaj poniższe czynności lub po prostu skopiuj [ChatRoom. Razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Dodaj link do strony i odwołanie.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Dodaj kod do nowego klienta sygnalizującego, aby wysyłać i odbierać wiadomości.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Dodaj część renderowania przed `@code` interfejsem użytkownika do współpracy z klientem sygnalizującym.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Aktualizuj `NavMenu.razor` , aby wstawić menu wprowadzania dla pokoju rozmów w obszarze `NavMenuCssClass` jak Rest.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Aktualizuj `site.css` , aby zoptymalizować widoki bąbelków obszaru rozmowy. Dołącz Poniższy kod na końcu.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Kliknij przycisk <kbd>F5</kbd> , aby uruchomić aplikację. Będziesz w stanie rozmawiać jak poniżej.

   [![Zostanie wyświetlona animowana rozmowa między Robert i Alicja. Alicja mówi Witaj, Robert mówi Witaj. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

   Do tej pory aplikacja Blazor działa na lokalnym sygnale i po wdrożeniu w usłudze Azure App Service zaleca się korzystanie z [usługi Azure sygnalizującej](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) , która umożliwia skalowanie aplikacji serwera Blazor na dużą liczbę współbieżnych połączeń sygnałów. Ponadto globalne zasięgi i wysokiej wydajności centrów danych usługi sygnalizujących znacznie ułatwiają zredukowanie opóźnień ze względu na lokalizację geograficzną.

> [!IMPORTANT]
> W aplikacji serwera Blazor Stany UI są utrzymywane po stronie serwera, co oznacza, że w tym przypadku jest wymagany serwer programu. Jeśli istnieje pojedynczy serwer aplikacji, serwer jest zapewniany przez program. Jeśli jednak istnieje wiele serwerów aplikacji, istnieje możliwość, że negocjacje i połączenie z klientem mogą należeć do różnych serwerów i prowadzić do błędów interfejsu użytkownika w aplikacji Blazor. W związku z tym należy włączyć serwer programu w następujący sposób `appsettings.json` :
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Kliknij prawym przyciskiem myszy projekt i przejdź do `Publish` .

   * Obiekt docelowy: Azure
   * Określony element docelowy: obsługiwane są wszystkie typy **Azure App Service** .
   * App Service: Utwórz nową lub wybierz istniejącą usługę App Service.

   [![Animacja pokazuje wybór platformy Azure jako docelowej, a następnie usługa Azure App usługi jako element docelowy. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Dodaj zależność usługi Azure Signal

   Po utworzeniu profilu publikowania zobaczysz zalecany komunikat w obszarze **zależności usługi**. Kliknij pozycję **Konfiguruj** , aby utworzyć nową lub wybrać istniejącą usługę Azure Signal w panelu.

   [![Po opublikowaniu link do konfiguracji jest wyróżniony. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   W zależności od usługi można wykonać następujące czynności, aby umożliwić aplikacji automatyczne przełączenie do usługi Azure sygnalizującej na platformie Azure.

   * Aktualizuj [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) , aby korzystać z usługi Azure Signal Service.
   * Dodaj odwołanie do pakietu NuGet usługi Azure Signal Service.
   * Zaktualizuj właściwości profilu, aby zapisać ustawienia zależności.
   * Konfigurowanie magazynu kluczy tajnych zależy od wybranej opcji.
   * Dodaj `appsettings` konfigurację, aby wybrać aplikację docelową platformy Azure.

   [![W przypadku podsumowania zmian pola wyboru są używane do zaznaczania wszystkich zależności. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Publikowanie aplikacji

   Teraz wszystko jest gotowe do opublikowania. Po zakończeniu publikowania zostanie ona zawarta w przeglądarce. 
   > [!NOTE]
   > Może nie być od razu zadziałała na stronie po raz pierwszy, ponieważ Azure App Service wdrożenie rozpoczyna opóźnienie i spróbuj odświeżyć stronę, aby dać kilka opóźnień.
   > Oprócz tego można użyć trybu debugera przeglądarki z <kbd>klawiszem F12</kbd> , aby sprawdzić, czy ruch został już przekierowany do usługi Azure signaler.

   [![Przykład rozmowy sygnalizującej Blazor ma pole tekstowe dla swojej nazwy i przycisk Rozmowa!, aby rozpocząć rozmowę. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Dodatkowy temat: Włączanie usługi Azure Signal Service w programowaniu lokalnym

1. Dodaj odwołanie do zestawu SDK usługi Azure Signal

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Dodaj wywołanie do usługi Azure Sygnalizacyjnyej w systemie `Startup.ConfigureServices()` .

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Konfigurowanie usługi Azure Signal `ConnectionString` w programie `appsetting.json` lub za pomocą narzędzia do [zarządzania kluczami tajnymi](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager)

> [!NOTE]
> Krok 2 można zastąpić za pomocą programu [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) do zestawu sygnałów SDK.
> 
> 1. Dodaj konfigurację, aby włączyć usługę Azure Signal Service w `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Przypisz zestaw startowy hostingu, aby użyć zestawu Azure Signal SDK. Edytuj `launchSettings.json` i Dodaj konfigurację, jak poniżej `environmentVariables` .
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Masz problemy? Daj nam znać.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, usuń grupę zasobów za pomocą witryny Azure Portal.

## <a name="next-steps"></a>Następne kroki

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie prostego pokoju rozmów przy użyciu aplikacji Blazor Server.
> * Zmodyfikuj składniki Razor.
> * Użyj obsługi zdarzeń i powiązania danych w składnikach.
> * Szybkie wdrażanie do Azure App Service w programie Visual Studio.
> * Przeprowadź migrację lokalnego sygnału do usługi Azure Signal Service.

Dowiedz się więcej o wysokiej dostępności.
> [!div class="nextstepaction"]
> [Odporność i odzyskiwanie po awarii](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [ASP.NET Core Blazor](/aspnet/core/blazor)
