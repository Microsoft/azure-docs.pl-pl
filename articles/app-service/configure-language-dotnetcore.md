---
title: Konfigurowanie aplikacji ASP.NET Core systemu Windows
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core w natywnych wystąpieniach systemu Windows App Service. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908147"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Konfigurowanie aplikacji ASP.NET Core systemu Windows dla Azure App Service

> [!NOTE]
> Aby uzyskać ASP.NET w .NET Framework, zobacz [Konfigurowanie aplikacji ASP.NET dla Azure App Service](configure-language-dotnet-framework.md)

Aplikacje ASP.NET Core należy wdrożyć do Azure App Service jako skompilowane pliki binarne. Narzędzie do publikowania programu Visual Studio kompiluje rozwiązanie, a następnie wdraża skompilowane pliki binarne bezpośrednio, natomiast aparat wdrażania App Service najpierw wdraża repozytorium kodu, a następnie kompiluje pliki binarne. Aby uzyskać informacje na temat aplikacji systemu Linux, zobacz [Konfigurowanie aplikacji ASP.NET Core systemu Linux dla Azure App Service](containers/configure-language-dotnetcore.md).

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla ASP.NET Core deweloperów. Jeśli nie korzystasz z Azure App Service, najpierw postępuj zgodnie z samouczkiem [Szybki Start](app-service-web-get-started-dotnet.md) i [ASP.NET Core z programem SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Pokaż obsługiwane wersje środowiska uruchomieniowego platformy .NET Core

W App Service wystąpienia systemu Windows mają już zainstalowane wszystkie obsługiwane wersje programu .NET Core. Aby wyświetlić dostępne dla Ciebie wersje środowiska uruchomieniowego platformy .NET Core i zestawu SDK, przejdź do `https://<app-name>.scm.azurewebsites.net/DebugConsole` i uruchom następujące polecenie w konsoli opartej na przeglądarce:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Ustaw wersję .NET Core

Ustaw platformę docelową w pliku projektu dla projektu ASP.NET Core. Aby uzyskać więcej informacji, zobacz [Wybieranie wersji platformy .NET Core do użycia](https://docs.microsoft.com/dotnet/core/versions/selection) w dokumentacji platformy .NET Core.

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W App Service można [ustawić ustawienia aplikacji](configure-common.md#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp w dowolnej klasie przy użyciu wzorca iniekcji zależności standardowej ASP.NET Core:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

W przypadku skonfigurowania ustawienia aplikacji o tej samej nazwie w App Service i w *appsettings.jsna*przykład wartość App Service ma pierwszeństwo przed *appsettings.js* wartości. Lokalna *appsettings.jsna* wartość umożliwia debugowanie aplikacji lokalnie, ale wartość App Service umożliwia uruchamianie aplikacji w produkcie z ustawieniami produkcyjnymi. Parametry połączenia działają w ten sam sposób. W ten sposób można zachować wpisy tajne aplikacji poza repozytorium kodu i uzyskać dostęp do odpowiednich wartości bez konieczności zmiany kodu.

> [!NOTE]
> Należy zauważyć, że [dane hierarchicznej konfiguracji](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) w *appsettings.js* są dostępne przy użyciu `:` ogranicznika standardowego dla programu .NET Core. Aby zastąpić określone ustawienie konfiguracji hierarchicznej w App Service, w kluczu Ustaw nazwę ustawienia aplikacji o takim samym formacie. w [Cloud Shell](https://shell.azure.com)można uruchomić następujący przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Wdróż rozwiązania obejmujące wiele projektów

Gdy rozwiązanie programu Visual Studio zawiera wiele projektów, proces publikacji programu Visual Studio zawiera już wybór projektu do wdrożenia. Po wdrożeniu programu do aparatu wdrażania App Service, takiego jak Git lub with ZIP Deploy, z włączonym automatyzacją kompilacji, aparat wdrażania App Service wybiera pierwszą witrynę sieci Web lub projekt aplikacji sieci Web, który znajduje się w aplikacji App Service. Można określić, który App Service projektu powinien być używany przez określenie `PROJECT` Ustawienia aplikacji. Na przykład uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

ASP.NET Core udostępnia [wbudowanego dostawcę rejestrowania dla App Service](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). W *program.cs* projektu Dodaj dostawcę do aplikacji za pomocą `ConfigureLogging` metody rozszerzającej, jak pokazano w następującym przykładzie:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Następnie można konfigurować i generować dzienniki przy użyciu [standardowego wzorca .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji na temat rozwiązywania problemów z ASP.NET Core aplikacjami w App Service, zobacz [Rozwiązywanie problemów ASP.NET Core na Azure App Service i usługach IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Strona pobierania szczegółowych wyjątków

Gdy aplikacja ASP.NET Core generuje wyjątek w debugerze programu Visual Studio, przeglądarka wyświetla szczegółową stronę wyjątku, ale w App Service tej stronie jest zastępowany ogólnym błędem **HTTP 500** lub **Wystąpił błąd podczas przetwarzania żądania.** . Aby wyświetlić szczegółową stronę wyjątku w App Service, Dodaj `ASPNETCORE_ENVIRONMENT` ustawienie aplikacji do aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi wiedzieć, czy żądania użytkowników są szyfrowane, należy skonfigurować oprogramowanie pośredniczące w programie *Startup.cs*:

- Skonfiguruj oprogramowanie pośredniczące w programie [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .
- Dodaj zakresy prywatnych adresów IP do znanych sieci, dzięki czemu oprogramowanie pośredniczące może ufać App Service Module równoważenia obciążenia.
- Wywołaj metodę [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) w elemencie `Startup.Configure` przed wywołaniem innych middlewares.

Umieszczenie wszystkich trzech elementów razem, kod wygląda podobnie do poniższego przykładu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
