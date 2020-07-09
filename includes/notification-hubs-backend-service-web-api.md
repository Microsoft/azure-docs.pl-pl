---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081427"
---
### <a name="create-a-web-project"></a>Tworzenie projektu sieci Web

1. W programie **Visual Studio**wybierz pozycję **plik**  >  **nowe rozwiązanie**.

1. Wybierz **.NET Core**pozycję  >  interfejs API**aplikacji**.NET Core  >  **ASP.NET Core**  >  **API**  >  **dalej**.
  
1. W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz pozycję **docelowa platforma** **.NET Core 3,1**.

1. Wprowadź *PushDemoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.

1. Rozpocznij debugowanie (**Command**  +  **wprowadzanie**polecenia), aby przetestować aplikację z szablonem.

    > [!NOTE]
    > Aplikacja z szablonem jest skonfigurowana do używania **WeatherForecastController** jako *launchUrl*. Ta wartość jest ustawiana we **właściwościach**  >  **launchSettings.json**.  
    >
    > Jeśli zostanie wyświetlony monit z komunikatem o **nieprawidłowym wykrytym certyfikacie deweloperskim** :
    >
    > 1. Kliknij przycisk **tak** , aby wyrazić zgodę na uruchomienie narzędzia "dotnet dev-certs https", aby rozwiązać ten problem. Narzędzie "dotnet dev-certs https" wyświetla monit o podanie hasła do certyfikatu oraz hasła dla łańcucha kluczy.
    >
    > 1. Kliknij przycisk **tak** po wyświetleniu monitu o **zainstalowanie i zaufanie nowego certyfikatu**, a następnie wprowadź hasło do łańcucha kluczy.

1. Rozwiń folder **controllers** , a następnie usuń **WeatherForecastController.cs**.

1. Usuń **WeatherForecast.cs**.

1. **Kontrolka**  +  **Kliknij** projekt **PushDemoApi** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Skonfiguruj lokalne wartości konfiguracji przy użyciu [Narzędzia do zarządzania kluczami tajnymi](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager). Oddzielenie wpisów tajnych z rozwiązania gwarantuje, że nie zakończą się one w kontroli źródła. Otwórz **Terminal** , a następnie przejdź do katalogu pliku projektu i uruchom następujące polecenia:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Zastąp wartości symboli zastępczych własnymi nazwami centrum powiadomień i wartościami parametrów połączenia. Zostały one zanotowane w sekcji [Tworzenie centrum powiadomień](#create-a-notification-hub) . W przeciwnym razie możesz je wyszukać na [platformie Azure](https://portal.azure.com).

    **NotificationsHub: Nazwa**:  
    Zobacz *name (nazwa* ) w podsumowaniu **Essentials** w górnej części **omówienia**.  

    **NotificationHub: ConnectionString**:  
    Zobacz *DefaultFullSharedAccessSignature* w **zasadach dostępu**

    > [!NOTE]
    > W przypadku scenariuszy produkcyjnych można zapoznać się z opcjami, takimi jak [Magazyn kluczy platformy Azure](https://azure.microsoft.com/services/key-vault) , aby bezpiecznie przechowywać parametry połączenia. Dla uproszczenia wpisy tajne zostaną dodane do ustawień aplikacji [Azure App Service](https://azure.microsoft.com/services/app-service/) .

### <a name="authenticate-clients-using-an-api-key-optional"></a>Uwierzytelnianie klientów przy użyciu klucza interfejsu API (opcjonalnie)

Klucze interfejsu API nie są tak bezpieczne jak tokeny, ale będą wystarczające do celów tego samouczka. Klucz interfejsu API można łatwo skonfigurować za pośrednictwem [oprogramowania pośredniczącego ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Dodaj **klucz interfejsu API** do lokalnych wartości konfiguracyjnych.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Należy zamienić wartość symbolu zastępczego na własną i zanotować ją.

1. **Kontrolka**  +  **Kliknij** projekt **PushDemoApi** , wybierz pozycję **Nowy folder** z menu **Dodaj** , a następnie kliknij pozycję **Dodaj** przy użyciu *uwierzytelniania* jako **nazwę folderu**.

1. **Kontrolka**  +  **Kliknij** folder **uwierzytelnianie** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Wybierz pozycję **Ogólne**  >  **pusta Klasa**, wprowadź *ApiKeyAuthOptions.cs* jako **nazwę**, a następnie kliknij pozycję **Nowy** Dodaj następującą implementację.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Dodaj kolejną **pustą klasę** do folderu **uwierzytelniania** o nazwie *ApiKeyAuthHandler.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [Program obsługi uwierzytelniania](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) jest typem, który implementuje zachowanie schematu, w tym przypadku niestandardowego schematu kluczy interfejsu API.

1. Dodaj kolejną **pustą klasę** do folderu **uwierzytelniania** o nazwie *ApiKeyAuthenticationBuilderExtensions.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Ta metoda rozszerzenia upraszcza kod konfiguracji oprogramowania pośredniczącego w programie **Startup.cs** , co sprawia, że jest bardziej czytelny i ogólnie łatwiejszy w obserwowanie.

1. W **Startup.cs**zaktualizuj metodę **ConfigureServices** , aby skonfigurować uwierzytelnianie klucza interfejsu API poniżej wywołania **usług. Metoda addcontrollers** .

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Nadal w **Startup.cs**, zaktualizuj metodę **Configure** , aby wywołać metody rozszerzenia **UseAuthentication** i **UseAuthorization** w **IApplicationBuilder**aplikacji. Upewnij się, że te metody są wywoływane po **UseRouting** i przed **aplikacją. UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Wywołanie **UseAuthentication** rejestruje oprogramowanie pośredniczące, które używa poprzednio zarejestrowanych schematów uwierzytelniania (z **ConfigureServices**). Ta nazwa musi być wywoływana przed wszystkimi programów pośredniczących, które są zależne od uwierzytelniania użytkowników.

### <a name="add-dependencies-and-configure-services"></a>Dodawanie zależności i Konfigurowanie usług

ASP.NET Core obsługuje wzorzec projektowania oprogramowania dla [iniekcji zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) , który jest techniką do osiągnięcia [niewersji kontroli (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.  

Korzystanie z centrum powiadomień i [zestawu Notification Hubs SDK dla operacji zaplecza](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) jest hermetyzowane w ramach usługi. Usługa jest zarejestrowana i udostępniana za pomocą odpowiedniego abstrakcji.

1. **Kontrolka**  +  **Kliknij** folder **zależności** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet.**...

1. Wyszukaj ciąg **Microsoft. Azure. NotificationHubs** i upewnij się, że jest on sprawdzony.

1. Kliknij przycisk **Dodaj pakiety**, a następnie kliknij przycisk **Akceptuj** po wyświetleniu monitu, aby zaakceptować postanowienia licencyjne.

1. **Kontrolka**  +  **Kliknij** projekt **PushDemoApi** , wybierz pozycję **Nowy folder** z menu **Dodaj** , a następnie kliknij pozycję **Dodaj** przy użyciu *modeli* jako **nazwę folderu**.

1. **Kontrolka**  +  **Kliknij** folder **modele** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Wybierz pozycję **Ogólne**  >  **pusta Klasa**, wprowadź *PushTemplates.cs* jako **nazwę**, a następnie kliknij pozycję **Nowy** Dodaj następującą implementację.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Ta klasa zawiera ładunki powiadomień z tokenami dla ogólnych i dyskretnych powiadomień wymaganych przez ten scenariusz. Ładunki są zdefiniowane poza [instalacją](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) , aby umożliwić eksperymentowanie bez konieczności aktualizowania istniejących instalacji za pośrednictwem usługi. Obsługa zmian w instalacjach w ten sposób jest poza zakresem dla tego samouczka. W przypadku produkcji należy rozważyć użycie [szablonów niestandardowych](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Wybierz pozycję **Ogólne**  >  **pusta Klasa**, wprowadź *DeviceInstallation.cs* jako **nazwę**, a następnie kliknij pozycję **Nowy** Dodaj następującą implementację.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Dodaj kolejną **pustą klasę** do folderu **models** o nazwie *NotificationRequest.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Dodaj kolejną **pustą klasę** do folderu **models** o nazwie *NotificationHubOptions.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Dodaj nowy folder do projektu **PushDemoApi** o nazwie *Services*.

1. Dodaj **pusty interfejs** do folderu **Services** o nazwie *INotificationService.cs*, a następnie Dodaj następującą implementację.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Dodaj **pustą klasę** do folderu **Services** o nazwie *NotificationHubsService.cs*, a następnie Dodaj następujący kod w celu zaimplementowania interfejsu **INotificationService** :

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > Wyrażenie tagu przekazane do **SendTemplateNotificationAsync** jest ograniczone do 20 tagów. Jest ona ograniczona do 6 dla większości operatorów, ale wyrażenie zawiera tylko ORs (| |) w tym przypadku. Jeśli w żądaniu znajduje się więcej niż 20 tagów, muszą one być podzielone na wiele żądań. Zobacz dokumentację dotyczącą [routingu i znaczników tagów](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) , aby uzyskać więcej szczegółów.

1. W **Startup.cs**zaktualizuj metodę **ConfigureServices** , aby dodać **NotificationHubsService** jako pojedynczą implementację **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Tworzenie interfejsu API powiadomień

1. **Kontrolka**  +  **Kliknij** folder **controllers** , a następnie wybierz pozycję **nowy plik...** z menu **Dodaj** .

1. Wybierz pozycję **ASP.NET Core**  >  **Klasa kontrolera interfejsu API sieci Web**, w polu **Nazwa**wprowadź *NotificationsController* , a następnie kliknij przycisk **Nowy**.

1. Dodaj następujące przestrzenie nazw na początku pliku.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Zaktualizuj kontroler szablonu, aby dziedziczył po **ControllerBase** i ma atrybut **ApiController** .

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > Klasa bazowa **kontrolera** zapewnia pomoc techniczną dla widoków, ale nie jest ona wymagana w tym przypadku, dlatego można użyć **ControllerBase** zamiast tego.

1. W przypadku wybrania opcji ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) należy również dekorować **NotificationsController** z atrybutem **Autoryzuj** .

    ```cs
    [Authorize]
    ```

1. Zaktualizuj Konstruktor, aby akceptował zarejestrowane wystąpienie **INotificationService** jako argument i przypisać go do elementu członkowskiego tylko do odczytu.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. W **launchSettings.jsna** (w folderze **Właściwości** ) Zmień wartość **launchUrl** z `weatherforecast` na *API/Notifications* , aby pasowała do adresu URL określonego w atrybucie **RegistrationsController** **Route** .

1. Rozpocznij debugowanie (**Command**  +  **wprowadź**polecenie), aby zweryfikować, że aplikacja pracuje z nowym **NotificationsController** i zwraca stan **nieautoryzowany 401** .

    > [!NOTE]
    > Program Visual Studio może nie uruchamiać automatycznie aplikacji w przeglądarce. Aby przetestować interfejs API z tego punktu w programie, należy użyć programu [Poster](https://www.postman.com/downloads) .

1. Na nowej karcie **[Poster](https://www.postman.com/downloads)** Ustaw żądanie do **pobrania** i wprowadź adres poniżej.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Adres localhost powinien być zgodny z wartością **applicationUrl** znalezioną we **właściwościach**  >  **launchSettings.js**. Wartość domyślna powinna być `https://localhost:5001;http://localhost:5000` jednak taka, aby sprawdzić, czy otrzymasz odpowiedź 404.

1. Jeśli wybrano opcję ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) , należy skonfigurować nagłówki żądania, aby zawierały wartość **apikey** .

   | Klucz                            | Wartość                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Kliknij przycisk **Wyślij** .

    > [!NOTE]
    > Powinien zostać wyświetlony stan **200 OK** z zawartością **JSON** .
    >
    > Jeśli zostanie wyświetlone ostrzeżenie dotyczące **weryfikacji certyfikatu SSL** , można **[zmienić ustawienie](https://www.postman.com/downloads)** Zażądaj weryfikacji certyfikatu SSL w **ustawieniach**.

1. Zamień metody klasy z szablonem na następujący kod.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Tworzenie aplikacji interfejsu API

Teraz utworzysz [aplikację interfejsu API](https://azure.microsoft.com/services/app-service/api/) w [Azure App Service](https://docs.microsoft.com/azure/app-service/) na potrzeby hostowania usługi zaplecza.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Kliknij pozycję **Utwórz zasób**, następnie wyszukaj i wybierz pozycję **aplikacja interfejsu API**, a następnie kliknij pozycję **Utwórz**.

1. Zaktualizuj następujące pola, a następnie kliknij przycisk **Utwórz**.

    **Nazwa aplikacji:**  
    Wprowadź globalnie unikatową nazwę **aplikacji interfejsu API**

    **Ramach**  
    Wybierz tę samą **subskrypcję** docelową, w której utworzono centrum powiadomień.

    **Grupa zasobów:**  
    Wybierz tę samą **grupę zasobów** , w której utworzono centrum powiadomień.

    **App Service plan/lokalizacja:**  
    Utwórz nowy **Plan App Service**  

    > [!NOTE]
    > Zmień z opcji domyślnej na plan, który obejmuje obsługę **protokołu SSL** . W przeciwnym razie podczas pracy z aplikacją mobilną należy wykonać odpowiednie czynności, aby zapobiec blokowaniu żądań **http** .

    **Application Insights:**  
    Zachowaj sugerowaną opcję (nowy zasób zostanie utworzony przy użyciu tej nazwy) lub wybierz istniejący zasób.

1. Po zainicjowaniu obsługi administracyjnej **aplikacji interfejsu API** przejdź do tego zasobu.

1. Zanotuj Właściwość **adresu URL** w podsumowaniu **Essentials** w górnej części **przeglądu**. Ten adres URL jest *punktem końcowym zaplecza* , który będzie używany w dalszej części tego samouczka.

    > [!NOTE]
    > Adres URL używa określonej wcześniej nazwy aplikacji interfejsu API w formacie `https://<app_name>.azurewebsites.net` .

1. Z listy wybierz pozycję **Konfiguracja** (w obszarze **Ustawienia**).  

1. Dla każdego z poniższych ustawień kliknij pozycję **nowe ustawienie aplikacji** , aby wprowadzić **nazwę** i **wartość**, a następnie kliknij przycisk **OK**.

   | Nazwa                               | Wartość                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Są to te same ustawienia, które zostały zdefiniowane wcześniej w ustawieniach użytkownika. Powinno być możliwe ich skopiowanie. Ustawienie **uwierzytelnianie: ApiKey** jest wymagane tylko wtedy, gdy wybrano opcję ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) . W przypadku scenariuszy produkcyjnych można zapoznać się z opcjami, takimi jak [Azure — magazyn](https://azure.microsoft.com/services/key-vault)kluczy. Zostały one dodane jako ustawienia aplikacji dla uproszczenia w tym przypadku.

1. Po dodaniu wszystkich ustawień aplikacji kliknij przycisk **Zapisz**, a następnie **Kontynuuj**.

### <a name="publish-the-backend-service"></a>Publikowanie usługi wewnętrznej bazy danych

Następnie należy wdrożyć aplikację w aplikacji interfejsu API, aby była dostępna ze wszystkich urządzeń.  

1. Jeśli jeszcze tego nie zrobiono, Zmień konfigurację z **Debuguj** na **Release** .

1. **Kontrolka**  +  **Kliknij** projekt **PushDemoApi** , a następnie wybierz pozycję **Publikuj na platformie Azure...** z menu **Publikuj** .

1. Jeśli zostanie wyświetlony monit, postępuj zgodnie z przepływem uwierzytelniania. Użyj konta, które zostało użyte w poprzedniej sekcji [Tworzenie aplikacji interfejsu API](#create-the-api-app) .

1. Wybierz **aplikację interfejsu API Azure App Service** utworzoną wcześniej z listy jako element docelowy publikowania, a następnie kliknij przycisk **Publikuj**.

Po zakończeniu pracy z kreatorem program opublikuje aplikację na platformie Azure, a następnie otworzy aplikację. Zanotuj **adres URL** , jeśli jeszcze tego nie zrobiono. Ten adres URL jest *punktem końcowym zaplecza* używanym w dalszej części tego samouczka.

### <a name="validating-the-published-api"></a>Weryfikowanie opublikowanego interfejsu API

1. W obszarze **[Ogłoś](https://www.postman.com/downloads)** Otwórz nową kartę, ustaw żądanie na **wpis** i wprowadź adres poniżej. Zastąp symbol zastępczy adresem bazowym zanotowanym w poprzedniej sekcji [publikowanie usługi zaplecza](#publish-the-backend-service) .

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Adres podstawowy powinien mieć format``https://<app_name>.azurewebsites.net/``

1. Jeśli wybrano opcję ukończenia [uwierzytelniania klientów przy użyciu klucza interfejsu API](#authenticate-clients-using-an-api-key-optional) , należy skonfigurować nagłówki żądania, aby zawierały wartość **apikey** .

   | Klucz                            | Wartość                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Wybierz opcję **RAW** dla **treści**, a następnie wybierz pozycję **JSON** z listy opcje formatu, a następnie Dołącz niepewną zawartość **JSON** :

    ```json
    {}
    ```

1. Kliknij pozycję **Wyślij**.

    > [!NOTE]
    > W usłudze powinien zostać wyświetlony **niewłaściwy stan żądania 400** .

1. Wykonaj ponownie kroki 1-4, ale tym razem określając punkt końcowy żądania, aby potwierdzić, że otrzymasz taką samą odpowiedź **400 nieprawidłowego żądania** .

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Nie jest jeszcze możliwe przetestowanie interfejsu API przy użyciu prawidłowych danych żądania, ponieważ będzie to wymagało informacji specyficznych dla platformy z aplikacji mobilnej klienta.
