---
title: Konfigurowanie aplikacji ASP.NET Core
description: Dowiedz się, jak skonfigurować aplikację ASP.NET Core w natywnych wystąpieniach systemu Windows lub w kontenerze z wstępnie skompilowanym systemem Linux w programie Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 1223ff5c56d3c7d58b324d2099980bc0b5408125
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655972"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Konfigurowanie aplikacji ASP.NET Core dla Azure App Service

> [!NOTE]
> Aby uzyskać ASP.NET w .NET Framework, zobacz [Konfigurowanie aplikacji ASP.NET dla Azure App Service](configure-language-dotnet-framework.md)

Aplikacje ASP.NET Core należy wdrożyć do Azure App Service jako skompilowane pliki binarne. Narzędzie do publikowania programu Visual Studio kompiluje rozwiązanie, a następnie wdraża skompilowane pliki binarne bezpośrednio, natomiast aparat wdrażania App Service najpierw wdraża repozytorium kodu, a następnie kompiluje pliki binarne.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla ASP.NET Core deweloperów. Jeśli nie korzystasz z Azure App Service, postępuj zgodnie z przewodnikiem [szybki start ASP.NET Core](quickstart-dotnetcore.md) i [ASP.NET Core z samouczkiem SQL Database](tutorial-dotnetcore-sqldb-app.md) .

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Pokaż obsługiwane wersje środowiska uruchomieniowego platformy .NET Core

W App Service wystąpienia systemu Windows mają już zainstalowane wszystkie obsługiwane wersje programu .NET Core. Aby wyświetlić dostępne dla Ciebie wersje środowiska uruchomieniowego platformy .NET Core i zestawu SDK, przejdź do `https://<app-name>.scm.azurewebsites.net/DebugConsole` i uruchom następujące polecenie w konsoli opartej na przeglądarce:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>Pokaż wersję .NET Core

Aby wyświetlić bieżącą wersję programu .NET Core, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje programu .NET Core, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>Ustaw wersję .NET Core

::: zone pivot="platform-windows"  

Ustaw platformę docelową w pliku projektu dla projektu ASP.NET Core. Aby uzyskać więcej informacji, zobacz [Wybieranie wersji platformy .NET Core do użycia](/dotnet/core/versions/selection) w dokumentacji platformy .NET Core.

::: zone-end

::: zone pivot="platform-linux"

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić wersję .NET Core na 3,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Dostosuj automatyzację kompilacji

Jeśli aplikacja zostanie wdrożona za pomocą usługi Git lub zip z włączonym automatyzacją kompilacji, App Service kroki automatyzacji kompilacji w następującej kolejności:

1. Uruchom skrypt niestandardowy, jeśli został określony przez `PRE_BUILD_SCRIPT_PATH` .
1. Uruchom `dotnet restore` , aby przywrócić zależności NuGet.
1. Uruchom `dotnet publish` , aby utworzyć plik binarny dla środowiska produkcyjnego.
1. Uruchom skrypt niestandardowy, jeśli został określony przez `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` i `POST_BUILD_COMMAND` są zmiennymi środowiskowymi, które są domyślnie puste. Aby uruchomić polecenia przed kompilacją, zdefiniuj `PRE_BUILD_COMMAND` . Aby uruchomić polecenia po kompilacji, zdefiniuj `POST_BUILD_COMMAND` .

W poniższym przykładzie określono dwie zmienne do szeregu poleceń, oddzielone przecinkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Aby uzyskać dodatkowe zmienne środowiskowe umożliwiające dostosowanie automatyzacji kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Aby uzyskać więcej informacji na temat App Service uruchamiania i kompilowania ASP.NET Core aplikacji w systemie Linux, zobacz [dokumentację Oryx: jak wykrywane są i kompilowane aplikacje platformy .NET Core](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service można [określić ustawienia aplikacji](configure-common.md#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp w dowolnej klasie przy użyciu wzorca iniekcji zależności standardowej ASP.NET Core:

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

W przypadku skonfigurowania ustawienia aplikacji o tej samej nazwie w App Service i w *appsettings.jsna* przykład wartość App Service ma pierwszeństwo przed *appsettings.js* wartości. Lokalna *appsettings.jsna* wartość umożliwia debugowanie aplikacji lokalnie, ale wartość App Service umożliwia uruchamianie aplikacji w produkcie z ustawieniami produkcyjnymi. Parametry połączenia działają w ten sam sposób. W ten sposób można zachować wpisy tajne aplikacji poza repozytorium kodu i uzyskać dostęp do odpowiednich wartości bez konieczności zmiany kodu.

> [!NOTE]
> Należy zauważyć, że [dane hierarchicznej konfiguracji](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) w *appsettings.js* są dostępne przy użyciu `:` ogranicznika standardowego dla programu .NET Core. Aby zastąpić określone ustawienie konfiguracji hierarchicznej w App Service, w kluczu Ustaw nazwę ustawienia aplikacji o takim samym formacie. w [Cloud Shell](https://shell.azure.com)można uruchomić następujący przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Wdróż rozwiązania obejmujące wiele projektów

Gdy rozwiązanie programu Visual Studio zawiera wiele projektów, proces publikacji programu Visual Studio zawiera już wybór projektu do wdrożenia. Po wdrożeniu programu do aparatu wdrażania App Service, takiego jak Git lub with ZIP Deploy, z włączonym automatyzacją kompilacji, aparat wdrażania App Service wybiera pierwszą witrynę sieci Web lub projekt aplikacji sieci Web, który znajduje się w aplikacji App Service. Można określić, który App Service projektu powinien być używany przez określenie `PROJECT` Ustawienia aplikacji. Na przykład uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

ASP.NET Core udostępnia [wbudowanego dostawcę rejestrowania dla App Service](/aspnet/core/fundamentals/logging/#azure-app-service). W *program.cs* projektu Dodaj dostawcę do aplikacji za pomocą `ConfigureLogging` metody rozszerzającej, jak pokazano w następującym przykładzie:

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

Następnie można konfigurować i generować dzienniki przy użyciu [standardowego wzorca .NET Core](/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji na temat rozwiązywania problemów z ASP.NET Core aplikacjami w App Service, zobacz [Rozwiązywanie problemów ASP.NET Core na Azure App Service i usługach IIS](/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Strona pobierania szczegółowych wyjątków

Gdy aplikacja ASP.NET Core generuje wyjątek w debugerze programu Visual Studio, przeglądarka wyświetla szczegółową stronę wyjątku, ale w App Service tej stronie jest zastępowany ogólnym błędem **HTTP 500** lub **Wystąpił błąd podczas przetwarzania żądania.** . Aby wyświetlić szczegółową stronę wyjątku w App Service, Dodaj `ASPNETCORE_ENVIRONMENT` ustawienie aplikacji do aplikacji, uruchamiając następujące polecenie w <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi wiedzieć, czy żądania użytkowników są szyfrowane, należy skonfigurować oprogramowanie pośredniczące w programie *Startup.cs*:

- Skonfiguruj oprogramowanie pośredniczące w programie [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) , aby przekazywać `X-Forwarded-For` `X-Forwarded-Proto` nagłówki i w `Startup.ConfigureServices` .
- Dodaj zakresy prywatnych adresów IP do znanych sieci, dzięki czemu oprogramowanie pośredniczące może ufać App Service Module równoważenia obciążenia.
- Wywołaj metodę [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) w elemencie `Startup.Configure` przed wywołaniem innego oprogramowania pośredniczącego.

Umieszczenie wszystkich trzech elementów razem, kod wygląda podobnie do poniższego przykładu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        // These three subnets encapsulate the applicable Azure subnets. At the moment, it's not possible to narrow it down further.
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

Aby uzyskać więcej informacji, zobacz [konfigurowanie ASP.NET Core do pracy z serwerami proxy i usługami równoważenia obciążenia](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>Otwieranie sesji SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z SQL Database](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)

::: zone-end
