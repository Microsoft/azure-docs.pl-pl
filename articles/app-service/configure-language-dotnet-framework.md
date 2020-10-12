---
title: Konfigurowanie aplikacji ASP.NET
description: Dowiedz się, jak skonfigurować aplikację ASP.NET w Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 30fddaec9ca5d0439beadedf7c5ca6b6c7d51d83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961707"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Konfigurowanie aplikacji ASP.NET dla Azure App Service

> [!NOTE]
> Aby uzyskać ASP.NET Core, zobacz [Konfigurowanie aplikacji ASP.NET Core dla Azure App Service](configure-language-dotnetcore.md)

Aplikacje ASP.NET muszą zostać wdrożone w celu Azure App Service jako skompilowane pliki binarne. Narzędzie do publikowania programu Visual Studio kompiluje rozwiązanie, a następnie wdraża skompilowane pliki binarne bezpośrednio, natomiast aparat wdrażania App Service najpierw wdraża repozytorium kodu, a następnie kompiluje pliki binarne.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów ASP.NET. Jeśli nie korzystasz z Azure App Service, najpierw postępuj zgodnie z samouczkiem [Szybki Start](quickstart-dotnet-framework.md) i [ASP.NET z programem SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) .

## <a name="show-supported-net-framework-runtime-versions"></a>Pokaż obsługiwane wersje środowiska uruchomieniowego .NET Framework

W App Service wystąpienia systemu Windows mają już zainstalowane wszystkie obsługiwane wersje .NET Framework. Aby wyświetlić dostępne dla Ciebie wersje środowiska uruchomieniowego .NET Framework i zestawu SDK, przejdź do `https://<app-name>.scm.azurewebsites.net/DebugConsole` i uruchom odpowiednie polecenie w konsoli opartej na przeglądarce:

W przypadku wersji środowiska uruchomieniowego CLR 4 (.NET Framework 4 i nowsze):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

Najnowsza wersja .NET Framework może nie być natychmiast dostępna.

W przypadku wersji środowiska uruchomieniowego CLR 2 (.NET Framework 3,5 i poniżej):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Pokaż bieżącą wersję środowiska uruchomieniowego .NET Framework

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Wartość `v4.0` oznacza, że jest używana najnowsza wersja środowiska CLR 4 (.NET Framework 4. x). Wartość `v2.0` oznacza, że jest używana wersja środowiska CLR 2 (.NET Framework 3,5).

## <a name="set-net-framework-runtime-version"></a>Ustaw wersję środowiska uruchomieniowego .NET Framework

Domyślnie program App Service używa najnowszej obsługiwanej wersji .NET Framework do uruchamiania aplikacji ASP.NET. Aby uruchomić aplikację przy użyciu .NET Framework 3,5 zamiast tego, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) (v 2.0 oznacza środowisko CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W App Service można [ustawiać ustawienia aplikacji](configure-common.md#configure-app-settings) i parametry połączenia poza kodem aplikacji. Następnie można uzyskać do nich dostęp w dowolnej klasie przy użyciu standardowego wzorca ASP.NET:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

W przypadku skonfigurowania ustawienia aplikacji o tej samej nazwie w App Service i w *web.config*wartość App Service ma pierwszeństwo przed wartością *web.config* . Wartość *web.config* lokalnego umożliwia debugowanie aplikacji lokalnie, ale wartość App Service umożliwia uruchamianie aplikacji w produkcie z ustawieniami produkcyjnymi. Parametry połączenia działają w ten sam sposób. W ten sposób można zachować wpisy tajne aplikacji poza repozytorium kodu i uzyskać dostęp do odpowiednich wartości bez konieczności zmiany kodu.

## <a name="deploy-multi-project-solutions"></a>Wdróż rozwiązania obejmujące wiele projektów

Gdy rozwiązanie programu Visual Studio zawiera wiele projektów, proces publikacji programu Visual Studio zawiera już wybór projektu do wdrożenia. Po wdrożeniu programu do aparatu wdrażania App Service, takiego jak Git lub with ZIP Deploy, z włączonym automatyzacją kompilacji, aparat wdrażania App Service wybiera pierwszą witrynę sieci Web lub projekt aplikacji sieci Web, który znajduje się w aplikacji App Service. Można określić, który App Service projektu powinien być używany przez określenie `PROJECT` Ustawienia aplikacji. Na przykład uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Strona pobierania szczegółowych wyjątków

Gdy aplikacja ASP.NET generuje wyjątek w debugerze programu Visual Studio, przeglądarka wyświetla szczegółową stronę wyjątku, ale w App Service ta strona zostanie zastąpiona przez ogólny komunikat o błędzie. Aby wyświetlić szczegółową stronę wyjątku w App Service, Otwórz plik *Web.config* i Dodaj `<customErrors mode="Off"/>` element poniżej `<system.web>` elementu. Na przykład:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Wdróż ponownie aplikację ze zaktualizowanym *Web.config*. Powinna zostać wyświetlona taka sama Szczegółowa Strona wyjątku.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

W kodzie aplikacji można dodawać komunikaty diagnostyczne przy użyciu funkcji [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace). Na przykład: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)