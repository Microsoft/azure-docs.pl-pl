---
title: 'Szybki Start: Tworzenie aplikacji ASP.NET Core w języku C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service, wdrażając pierwszą aplikację ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 09/24/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperfq1
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: aa8eb945ba77e1a4ac5215acf3bdbc12cac0c4c9
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661129"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Szybki Start: Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure

::: zone pivot="platform-windows"  

W tym przewodniku szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację sieci Web ASP.NET Core w [Azure App Service](overview.md). 

Po zakończeniu będziesz mieć grupę zasobów platformy Azure składającą się z planu hostingu App Service i App Service ze wdrożoną aplikacją sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- W tym przewodniku szybki start wdrożono aplikację do App Service w systemie Windows. Aby wdrożyć program do App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej platformy .NET Core w programie App Service](./quickstart-dotnetcore.md).
- Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> przy użyciu obciążeń **ASP.NET i Web Development** .

  Jeśli zainstalowano już program Visual Studio 2019:

  - Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**.
  - Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.


## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Utwórz ASP.NET Core aplikację sieci Web w programie Visual Studio, wykonując następujące czynności:

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**wybierz pozycję **ASP.NET Core aplikacja sieci Web** i upewnij się, że w wybranym językach znajduje się język **C#** , a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**Nadaj nazwę projektowi aplikacji sieci Web *myFirstAzureWebApp*, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Możesz wdrożyć dowolny typ ASP.NET Core aplikacji sieci Web na platformie Azure, ale w tym przewodniku Szybki Start wybierz szablon **aplikacja sieci Web** . Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**i że żadna inna opcja nie jest zaznaczona. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie nowej aplikacji sieci Web ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację sieci Web lokalnie.

   ![Aplikacja internetowa uruchomiona lokalnie](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web

Aby opublikować aplikację internetową, musisz najpierw utworzyć i skonfigurować nową App Service, do której będzie można opublikować aplikację. 

W ramach konfigurowania App Service utworzysz:

- Nowa [Grupa zasobów](../azure-resource-manager/management/overview.md#terminology) zawierająca wszystkie zasoby platformy Azure dla usługi.
- Nowy [Plan hostingu](./overview-hosting-plans.md) , który określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.

Wykonaj następujące kroki, aby utworzyć App Service i opublikować aplikację sieci Web:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**. Jeśli jeszcze nie zalogowano się na koncie platformy Azure z poziomu programu Visual Studio, wybierz opcję **Dodaj konto** lub **Zaloguj się**. Możesz również utworzyć bezpłatne konto platformy Azure.

1. W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz pozycję **App Service**, wybierz pozycję **Utwórz nowy**, a następnie wybierz pozycję **Utwórz profil**.

   ![Wybieranie miejsca docelowego publikacji](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

1. W oknie dialogowym **App Service: Utwórz nowe** wprowadź globalnie unikatową **nazwę** aplikacji, akceptując nazwę domyślną lub wprowadzając nową nazwę. Prawidłowe znaki to: `a-z` , `A-Z` , `0-9` i `-` . Ta **Nazwa** jest używana jako prefiks adresu URL dla aplikacji sieci Web w formacie `http://<app_name>.azurewebsites.net` .

1. W obszarze **subskrypcja**Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W obszarze **Grupa zasobów**wybierz pozycję **Nowy**. W polu **Nazwa nowej grupy zasobów wprowadź nazwę** *zasobu* , a następnie wybierz **przycisk OK**. 

1. W obszarze **Plan hostingu**wybierz pozycję **Nowy**. 

1. W obszarze **Plan hostingu: Utwórz nowe** okno dialogowe, wprowadź wartości podane w poniższej tabeli:

   | Ustawienie  | Sugerowana wartość | Opis |
   | -------- | --------------- | ----------- |
   | **Plan hostingu**  | *myFirstAzureWebAppPlan* | Nazwa planu usługi App Service. |
   | **Lokalizacja**      | *West Europe* | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | **Rozmiar**          | *Bezpłatna* | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |
   
   ![Utwórz nowy plan hostingu](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Pozostaw **Application Insights** wartość *none*.

1. W oknie dialogowym **App Service: Utwórz nowy** wybierz pozycję **Utwórz** , aby rozpocząć tworzenie zasobów platformy Azure.

   ![Utwórz nową usługę App Service](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. Po zakończeniu działania kreatora wybierz pozycję **Publikuj**.

   ![Publikowanie aplikacji sieci Web na platformie Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Program Visual Studio publikuje aplikację sieci Web ASP.NET Core na platformie Azure i uruchamia aplikację w domyślnej przeglądarce. 

   ![Opublikowana aplikacja sieci Web ASP.NET uruchomiona na platformie Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

**Gratulacje!** Aplikacja sieci Web ASP.NET Core działa na żywo w Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Wykonaj następujące kroki, aby zaktualizować i ponownie wdrożyć aplikację internetową:

1. W **Eksplorator rozwiązań**w projekcie Otwórz **stronę**  >  **index. cshtml**.

1. Zastąp cały `<div>` tag następującym kodem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

1. Na stronie **Publikowanie** podsumowania wybierz pozycję **Publikuj**.

   ![Publikowanie aktualizacji w aplikacji sieci Web](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana aplikacja internetowa ASP.NET działająca na platformie Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby zarządzać swoją aplikacją internetową, przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

![Wybierz App Services](./media/quickstart-dotnetcore/app-services.png)

Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Zrzut ekranu przedstawiający stronę App Services z wybraną przykładową aplikacją sieci Web.":::

Strona **Przegląd** dla aplikacji sieci Web zawiera opcje podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera dalsze strony umożliwiające skonfigurowanie aplikacji.

![App Service w Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto programu Visual Studio do utworzenia i wdrożenia aplikacji sieci Web ASP.NET Core w Azure App Service.

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację platformy .NET Core i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[App Service w systemie Linux](overview.md#app-service-on-linux) oferuje wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. Ten przewodnik szybkiego startu pokazuje, jak utworzyć aplikację platformy [.NET Core](/aspnet/core/) w usłudze App Service w systemie Linux. Aplikację możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie kodu platformy .NET Core w aplikacji.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Kroki podane w tym artykule możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Zainstaluj najnowszą wersję zestawu SDK platformy .NET Core 3,1</a>

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>Lokalne tworzenie aplikacji

W oknie terminala na komputerze utwórz katalog o nazwie `hellodotnetcore` i zmień bieżący katalog na niego.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Utwórz nową aplikację .NET Core.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. 

Przywróć pakiety NuGet i uruchom aplikację.

```bash
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `http://localhost:5000`.

Na stronie zostanie wyświetlony komunikat **Hello World** z przykładowej aplikacji.

![Test przy użyciu przeglądarki](media/quickstart-dotnetcore/dotnet-browse-local.png)

W oknie terminalu naciśnij kombinację klawiszy **Ctrl + C**, aby zamknąć serwer sieci Web. Inicjowanie repozytorium Git dla projektu platformy .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Przejdź do nowo utworzonej aplikacji. Zastąp _ &lt; ciąg App-Name>_ nazwą aplikacji.

```bash
https://<app-name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja:

![Pusta strona aplikacji](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

Przykładowy kod .NET Core jest uruchamiany w usłudze App Service dla systemu Linux z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację platformy .NET Core w usłudze App Service w systemie Linux.

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>Aktualizowanie i ponowne wdrażanie kodu

W katalogu lokalnym otwórz plik _Startup.cs_. Wprowadź niewielką zmianę w tekście wywołania metody `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Zatwierdź zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "updated output"
git push azure master
```

Po zakończeniu wdrożenia przejdź z powrotem do okna przeglądarki otwartego w kroku **Przejdź do aplikacji** , a następnie kliknij przycisk Odśwież.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list.png" alt-text="Zrzut ekranu przedstawiający stronę App Services z wybraną przykładową aplikacją sieci Web.":::

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end