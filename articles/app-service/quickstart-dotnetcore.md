---
title: 'Szybki Start: Tworzenie aplikacji ASP.NET Core w języku C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service, wdrażając pierwszą aplikację ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 77e0768a617ef79ab8510f88bfdcd41d5647f9bf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701641"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Szybki Start: Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure

::: zone pivot="platform-windows"  

W tym przewodniku szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację sieci Web ASP.NET Core w [Azure App Service](overview.md). App Service obsługuje aplikacje platformy .NET 5,0.

Po zakończeniu będziesz mieć grupę zasobów platformy Azure składającą się z planu hostingu App Service i App Service ze wdrożoną aplikacją sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> przy użyciu obciążeń **ASP.NET i Web Development** .

  Jeśli zainstalowano już program Visual Studio 2019:

  - Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**. Najnowsze aktualizacje zawierają zestaw SDK programu .NET 5,0.
  - Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.


## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Utwórz ASP.NET Core aplikację sieci Web w programie Visual Studio, wykonując następujące czynności:

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt** wybierz pozycję **ASP.NET Core aplikacja sieci Web** i upewnij się, że w wybranym językach znajduje się język **C#** , a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę projektowi aplikacji sieci Web *myFirstAzureWebApp*, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Możesz wdrożyć dowolny typ ASP.NET Core aplikacji sieci Web na platformie Azure, ale w tym przewodniku Szybki Start wybierz szablon **aplikacja sieci Web** . Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** i że żadna inna opcja nie jest zaznaczona. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie nowej aplikacji sieci Web ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację sieci Web lokalnie.

   ![Aplikacja internetowa uruchomiona lokalnie](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt** wybierz pozycję **ASP.NET Core aplikacja sieci Web** i upewnij się, że w wybranym językach znajduje się język **C#** , a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę projektowi aplikacji sieci Web *myFirstAzureWebApp*, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. W przypadku aplikacji .NET 5,0 wybierz pozycję **ASP.NET Core 5,0** na liście rozwijanej.

1. Możesz wdrożyć dowolny typ aplikacji sieci Web ASP.NET Core na platformie Azure, ale w tym przewodniku Szybki Start wybierz szablon **aplikacji sieci web ASP.NET Core** . Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** i że żadna inna opcja nie jest zaznaczona. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie nowej aplikacji sieci Web ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację sieci Web lokalnie.

   ![Aplikacja internetowa uruchomiona lokalnie](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web

Aby opublikować aplikację internetową, musisz najpierw utworzyć i skonfigurować nową App Service, do której będzie można opublikować aplikację. 

W ramach konfigurowania App Service utworzysz:

- Nowa [Grupa zasobów](../azure-resource-manager/management/overview.md#terminology) zawierająca wszystkie zasoby platformy Azure dla usługi.
- Nowy [Plan hostingu](./overview-hosting-plans.md) , który określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.

Wykonaj następujące kroki, aby utworzyć App Service i opublikować aplikację sieci Web:

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**. 

1. W obszarze **Publikowanie** wybierz pozycję **Azure** , a następnie kliknij przycisk **dalej**.

1. Twoje opcje zależą od tego, czy użytkownik jest już zalogowany na platformie Azure i czy masz konto programu Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto** lub **Zaloguj** się, aby zalogować się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz odpowiednie konto.

   ![Logowanie do platformy Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Po prawej stronie **wystąpień App Service** kliknij pozycję **+** .

   ![Nowa aplikacja App Service](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. W obszarze **subskrypcja** Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W obszarze **Grupa zasobów** wybierz pozycję **Nowy**. W polu **Nazwa nowej grupy zasobów wprowadź nazwę** *zasobu* , a następnie wybierz **przycisk OK**. 

1. W obszarze **Plan hostingu** wybierz pozycję **Nowy**. 

1. W obszarze **Plan hostingu: Utwórz nowe** okno dialogowe, wprowadź wartości podane w poniższej tabeli:

   | Ustawienie  | Sugerowana wartość | Opis |
   | -------- | --------------- | ----------- |
   | **Plan hostingu**  | *myFirstAzureWebAppPlan* | Nazwa planu usługi App Service. |
   | **Lokalizacja**      | *West Europe* | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | **Rozmiar**          | *Bezpłatna* | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |
   
   ![Utwórz nowy plan hostingu](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. W polu **Nazwa** wprowadź unikatową nazwę aplikacji, która zawiera tylko prawidłowe znaki to `a-z` , `A-Z` , `0-9` i `-` . Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app-name>.azurewebsites.net`, gdzie `<app-name>` to nazwa aplikacji.

2. Wybierz pozycję **Utwórz** , aby utworzyć zasoby platformy Azure.

   ![Tworzenie zasobów aplikacji](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   Po zakończeniu działania kreatora zasoby platformy Azure są tworzone dla Ciebie i wszystko jest gotowe do opublikowania.

3. Wybierz pozycję **Zakończ** , aby zamknąć kreatora.

1. Na stronie **Publikowanie** kliknij pozycję **Publikuj**. Program Visual Studio kompiluje, pakuje i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

   ![Opublikowana aplikacja sieci Web ASP.NET uruchomiona na platformie Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

**Gratulacje!** Aplikacja sieci Web ASP.NET Core działa na żywo w Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Wykonaj następujące kroki, aby zaktualizować i ponownie wdrożyć aplikację internetową:

1. W **Eksplorator rozwiązań** w projekcie Otwórz **stronę**  >  **index. cshtml**.

1. Zastąp cały `<div>` tag następującym kodem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

1. Na stronie **Publikowanie** podsumowania wybierz pozycję **Publikuj**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

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
[App Service w systemie Linux](overview.md#app-service-on-linux) oferuje wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku szybki start pokazano, jak utworzyć aplikację [platformy .NET Core](/aspnet/core/) i wdrożyć ją w hostowanej App Service z systemem Linux przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Kroki podane w tym artykule możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>Konfigurowanie środowiska początkowego

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Zainstaluj najnowszą wersję zestawu SDK platformy .NET Core 3,1</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure</a>.

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Zainstaluj najnowszą wersję zestawu .net 5,0 SDK</a>.
* <a href="/cli/azure/install-azure-cli" target="_blank">Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure</a>.

---

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

```bash
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `http://localhost:5000`.

Na stronie zostanie wyświetlony komunikat **Hello World** z przykładowej aplikacji.

![Test przy użyciu przeglądarki](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Logowanie na platformie Azure
W oknie terminalu Zaloguj się do platformy Azure przy użyciu następującego polecenia:

```azurecli
az login
```

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż kod w folderze lokalnym (*hellodotnetcore*) przy użyciu `az webapp up` polecenia:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Jeśli `az` polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w artykule [Konfigurowanie początkowego środowiska](#set-up-your-initial-environment).
- Zamień na `<app-name>` nazwę, która jest unikatowa na całym systemie Azure (*prawidłowe znaki to `a-z` , `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- `--sku F1`Argument tworzy aplikację sieci Web w warstwie cenowej bezpłatna. Pomiń ten argument, aby użyć szybszej warstwy Premium, która wiąże się z godziną.
- Opcjonalnie możesz dołączyć argument, `--location <location-name>` gdzie `<location-name>` jest dostępny region platformy Azure. Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) polecenie.

Wykonanie polecenia może potrwać kilka minut. W trakcie korzystania z programu są dostępne komunikaty dotyczące tworzenia grupy zasobów, planu App Service i aplikacji hostingu, konfigurowania rejestrowania, a następnie wykonywania wdrożenia ZIP. Następnie zostanie wyświetlony komunikat "można uruchomić aplikację pod adresem http:// &lt; App-Name &gt; . azurewebsites.NET", który jest adresem URL aplikacji na platformie Azure.

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Przykładowe dane wyjściowe polecenia AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![Przykładowe dane wyjściowe polecenia AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

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

Zapisz zmiany, a następnie ponownie Wdróż aplikację przy użyciu `az webapp up` polecenia:

```azurecli
az webapp up --os-type linux
```

To polecenie używa wartości, które są buforowane lokalnie w pliku *. Azure/config* , łącznie z nazwą aplikacji, grupą zasobów i planem App Service.

Po zakończeniu wdrożenia przejdź z powrotem do okna przeglądarki otwartego w kroku **Przejdź do aplikacji** , a następnie kliknij przycisk Odśwież.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Zrzut ekranu przedstawiający stronę App Services z wybraną przykładową aplikacją platformy Azure.":::

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
