---
title: Samouczek — dostęp do aplikacji sieci Web Microsoft Graph jako użytkownik | Azure
description: W tym samouczku dowiesz się, jak uzyskać dostęp do danych w Microsoft Graph w imieniu zalogowanego użytkownika.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef007f045a5c53bf70f6d042167c157ab3f4decc
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428893"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Samouczek: dostęp do Microsoft Graph z zabezpieczonej aplikacji jako użytkownik

Dowiedz się, jak uzyskać dostęp do Microsoft Graph z aplikacji sieci Web działającej na Azure App Service.

:::image type="content" alt-text="Microsoft Graph dostępu" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Chcesz dodać dostęp do Microsoft Graph z aplikacji sieci Web i wykonać jakąś akcję jako zalogowany użytkownik. W tej sekcji opisano sposób udzielania delegowanych uprawnień aplikacji sieci Web i uzyskiwania informacji o profilu zalogowanego użytkownika z Azure Active Directory.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Przyznawanie delegowanych uprawnień do aplikacji sieci Web
> * Wywołaj Microsoft Graph z aplikacji sieci Web w imieniu zalogowanego użytkownika

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja sieci Web działająca na Azure App Service z [włączonym modułem uwierzytelniania App Service/autoryzacji](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Udziel dostępu frontonu do wywołania Microsoft Graph

Po włączeniu uwierzytelniania i autoryzacji w aplikacji sieci Web aplikacja sieci Web jest zarejestrowana na platformie tożsamości firmy Microsoft i jest obsługiwana przez aplikację usługi Azure AD. W tym kroku nadajesz uprawnienia aplikacji sieci Web dostępu Microsoft Graph w imieniu użytkownika. (Technicznie nadajesz aplikacji sieci Web usługi Azure AD uprawnienia dostępu do aplikacji usługi AD Microsoft Graph w imieniu użytkownika).

W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Azure Active Directory** lub wyszukaj i wybierz pozycję Azure Active Directory z dowolnej strony.

Wybierz **rejestracje aplikacji**  >  **posiadane aplikacje**  >  **Wyświetl wszystkie aplikacje w tym katalogu**. Wybierz nazwę aplikacji sieci Web, a następnie wybierz pozycję **uprawnienia interfejsu API**.

Wybierz pozycję **Dodaj uprawnienia** , a następnie wybierz pozycję Microsoft api i Microsoft Graph.

Wybierz pozycję **uprawnienia delegowane** i wybierz pozycję **użytkownik. Przeczytaj** z listy.  Kliknij pozycję **Dodaj uprawnienia**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurowanie usługi App Service do zwracania nadającego się do użycia tokenu dostępu

Aplikacja sieci Web ma teraz wymagane uprawnienia dostępu do Microsoft Graph jako zalogowany użytkownik. W tym kroku skonfigurujesz App Service uwierzytelnianie i autoryzacja, aby umożliwić dostęp do Microsoft Graph za pomocą tokenu dostępu. W tym kroku potrzebny jest identyfikator klienta/aplikacji usługi podrzędnej (Microsoft Graph). *00000003-0000-0000-C000-000000000000* to identyfikator aplikacji Microsoft Graph.

> [!IMPORTANT]
> Jeśli nie skonfigurujesz App Service w taki sposób, aby zwracał token dostępu, zostanie wyświetlony ```CompactToken parsing failed with error code: 80049217``` komunikat o błędzie podczas wywoływania Microsoft Graph interfejsów API w kodzie.

Przejdź do [Azure Resource Explorer](https://resources.azure.com/) i przy użyciu drzewa zasobów Znajdź swoją aplikację sieci Web.  Adres URL zasobu powinien wyglądać podobnie do: `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`

Azure Resource Explorer jest teraz otwarta z aplikacją sieci Web wybraną w drzewie zasobów. U góry strony kliknij pozycję **Odczyt/zapis** , aby włączyć edytowanie zasobów platformy Azure.

W lewej przeglądarce przejdź do szczegółów **konfiguracji**  >  **authsettings**.

W widoku **authsettings** kliknij pozycję **Edytuj**. Ustaw ```additionalLoginParams``` następujący ciąg JSON przy użyciu skopiowanego identyfikatora klienta.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Zapisz ustawienia, klikając pozycję **PUT**. To ustawienie może zająć kilka minut.  Aplikacja sieci Web jest teraz skonfigurowana do uzyskiwania dostępu do Microsoft Graph przy użyciu odpowiedniego tokenu dostępu.  Jeśli tego nie zrobisz, Microsoft Graph zwraca błąd informujący, że format kompaktowego tokenu jest niepoprawny.

## <a name="call-microsoft-graph-net"></a>Microsoft Graph wywołań (.NET)

Aplikacja sieci Web ma teraz wymagane uprawnienia i dodaje także identyfikator klienta Microsoft Graph do parametrów logowania. Za pomocą [biblioteki Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/)aplikacja internetowa pobiera token dostępu do uwierzytelniania za pomocą Microsoft Graph. W wersji 1.2.0 i nowszych Biblioteka Microsoft. Identity. Web Library integruje się z usługą i może działać obok App Service Module uwierzytelniania/autoryzacji.  Microsoft. Identity. Web wykrył, że aplikacja sieci Web jest hostowana w App Services i pobiera token dostępu z modułu App Services Authentication/Authorization.  Token dostępu jest następnie przesyłany do żądań uwierzytelnionych za pomocą interfejsu API Microsoft Graph.

> [!NOTE]
> Biblioteka Microsoft. Identity. Web nie jest wymagana w aplikacji sieci Web na potrzeby uwierzytelniania podstawowego/autoryzacji ani do uwierzytelniania żądań za pomocą Microsoft Graph.  Istnieje możliwość [bezpiecznego wywołania podrzędnych interfejsów API](tutorial-auth-aad.md#call-api-securely-from-server-code) z włączonym tylko App Service Module uwierzytelniania/autoryzacji.  
> Jednak uwierzytelnianie/autoryzacja App Service jest zaprojektowana na potrzeby bardziej podstawowych scenariuszy uwierzytelniania.  W przypadku bardziej złożonych scenariuszy (na przykład obsługi oświadczeń niestandardowych) potrzebna jest Biblioteka Microsoft. Identity. Web Library lub [Biblioteka uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview). Na początku istnieje nieco większa konfiguracja i konfiguracji, ale Biblioteka Microsoft. Identity. Web Library może działać obok modułu uwierzytelniania App Service/autoryzacji.  Później, gdy aplikacja sieci Web musi obsługiwać bardziej złożone scenariusze, można wyłączyć moduł uwierzytelniania App Service/autoryzacji i Microsoft. Identity. Web będzie już częścią aplikacji.

### <a name="install-client-library-packages"></a>Zainstaluj pakiety biblioteki klienta

Zainstaluj pakiety NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) i [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph) w projekcie przy użyciu interfejsu wiersza polecenia platformy .NET Core lub konsoli Menedżera pakietów w programie Visual Studio.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Otwórz wiersz polecenia i przejdź do katalogu, który zawiera plik projektu.

Uruchom polecenia instalacji:

```dotnetcli
dotnet add package Microsoft.Graph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Menedżer pakietów](#tab/package-manager)
Otwórz projekt/rozwiązanie w programie Visual Studio i Otwórz konsolę programu przy użyciu **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów** .

Uruchom polecenia instalacji:
```powershell
Install-Package Microsoft.Graph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

W pliku *Startup.cs* ```AddMicrosoftIdentityWebApp``` Metoda dodaje aplikację Microsoft. Identity. Web do aplikacji sieci Web.  ```AddMicrosoftGraph```Metoda dodaje obsługę Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* określa konfigurację biblioteki Microsoft. Identity. Web.  W [Azure Portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory** z menu portal i wybierz pozycję **rejestracje aplikacji**. Wybierz rejestrację aplikacji utworzoną po włączeniu modułu uwierzytelniania App Service/autoryzacji (Rejestracja aplikacji powinna mieć taką samą nazwę jak aplikacja sieci Web).  Identyfikator dzierżawy i identyfikator klienta można znaleźć na stronie Przegląd rejestracji aplikacji.  Nazwę domeny można znaleźć na stronie Przegląd Azure Active Directory dla Twojej dzierżawy.

Program *Graph* określa punkt końcowy Microsoft Graph i początkowy zakres wymagany przez aplikację.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

Poniższy przykład pokazuje, jak wywoływać Microsoft Graph jako zalogowany użytkownik i uzyskać informacje o użytkowniku.  ```GraphServiceClient```Obiekt jest wstrzykiwany do kontrolera, a uwierzytelnianie zostało skonfigurowane przez użytkownika w bibliotece Microsoft. Identity. Web.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wykonujesz ten samouczek i nie potrzebujesz już aplikacji sieci Web lub skojarzonych zasobów, [Wyczyść utworzone zasoby](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Przyznawanie delegowanych uprawnień do aplikacji sieci Web
> * Wywołaj Microsoft Graph z aplikacji sieci Web w imieniu zalogowanego użytkownika

> [!div class="nextstepaction"]
> [Usługa App Service uzyskuje dostęp do Microsoft Graph jako aplikację](scenario-secure-app-access-microsoft-graph-as-app.md)
