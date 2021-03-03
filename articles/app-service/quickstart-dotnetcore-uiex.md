---
title: 'Szybki Start: Tworzenie aplikacji ASP.NET Core w języku C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service, wdrażając pierwszą aplikację ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e6675dbc4381a3a21e9daa651948af17f1d70d7d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748581"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Szybki Start: Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure

::: zone pivot="platform-windows"  

W tym przewodniku szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację sieci Web ASP.NET Core <abbr title="Usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i aplikacji zaplecza mobilnego.">Azure App Service</abbr>. App Service obsługuje aplikacje platformy .NET 5,0.

Gdy skończysz, będziesz mieć platformę Azure <abbr title="Logiczny kontener dla powiązanych zasobów platformy Azure, którymi można zarządzać jako jednostką.">grupa zasobów</abbr>, składający się z <abbr title="Plan określający lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.">Plan usługi App Service</abbr> i <abbr title="Reprezentacja aplikacji sieci Web, która zawiera kod aplikacji, nazwy hostów DNS, certyfikaty i powiązane zasoby.">Aplikacja usługi App Service</abbr> ze wdrożoną przykładową aplikacją ASP.NET Core.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

- **Pobierz konto platformy Azure** z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- **Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a>** przy użyciu obciążeń **ASP.NET i Web Development** .

<details>
<summary>Masz już program Visual Studio 2019?</summary>
  Jeśli zainstalowano już program Visual Studio 2019:

  - **Zainstaluj najnowsze aktualizacje** programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**. Najnowsze aktualizacje zawierają zestaw SDK programu .NET 5,0.
  - **Dodaj obciążenie** , wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Utwórz aplikację sieci Web ASP.NET Core

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt** wybierz pozycję **ASP.NET Core aplikacja sieci Web** i upewnij się, że w wybranym językach znajduje się język **C#** , a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę projektowi aplikacji sieci Web *myFirstAzureWebApp*, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. W przypadku aplikacji .NET 5,0 wybierz pozycję **ASP.NET Core 5,0** na liście rozwijanej. W przeciwnym razie użyj wartości domyślnej.

1. Możesz wdrożyć dowolny typ aplikacji sieci Web ASP.NET Core na platformie Azure, ale w tym przewodniku Szybki Start wybierz szablon **aplikacji sieci web ASP.NET Core** . Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania** i że żadna inna opcja nie jest zaznaczona. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie nowej aplikacji sieci Web ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację sieci Web lokalnie.

   ![Aplikacja internetowa uruchomiona lokalnie](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. Opublikuj aplikację sieci Web

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

   | Ustawienie  | Sugerowana wartość |
   | -------- | --------------- |
   | **Plan hostingu**  | *myFirstAzureWebAppPlan* |
   | **Lokalizacja**      | *West Europe* |
   | **Rozmiar**          | *Bezpłatna* |
   
   ![Utwórz nowy plan hostingu](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. W polu **Nazwa** wprowadź unikatową nazwę aplikacji.

    <details>
        <summary>Które znaki można użyć?</summary>
        Prawidłowe znaki to a-z, A-Z, 0-9 i-. Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji sieci Web to http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , gdzie <code>&lt;app-name&gt;</code> jest nazwą aplikacji.
    </details>

1. Wybierz pozycję **Utwórz** , aby utworzyć zasoby platformy Azure. 

   ![Tworzenie zasobów aplikacji](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Zaczekaj, aż Kreator ukończy tworzenie zasobów platformy Azure. Wybierz pozycję **Zakończ** , aby zamknąć kreatora.

1. Na stronie **Publikowanie** kliknij pozycję **Publikuj** , aby wdrożyć projekt. 

    <details>
        <summary>Co to jest Visual Studio?</summary>
        Program Visual Studio kompiluje, pakuje i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.
    </details>

   ![Opublikowana aplikacja sieci Web ASP.NET uruchomiona na platformie Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. zaktualizuj aplikację i Wdróż ponownie

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

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. zarządzanie aplikacją platformy Azure

1. Przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

    ![Wybierz App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Zrzut ekranu przedstawiający stronę App Services z wybraną przykładową aplikacją sieci Web.":::

1. Strona **Przegląd** dla aplikacji sieci Web zawiera opcje podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera dalsze strony umożliwiające skonfigurowanie aplikacji.

    ![App Service w Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Wyczyść zasoby

1. W menu Azure Portal lub stronie **głównej** wybierz pozycję **grupy zasobów**. Następnie na stronie **grupy zasobów** wybierz pozycję Grupa **zasobów**.

1. Na stronie Moja **zasobów** upewnij się, że wymienione zasoby są tymi, które chcesz usunąć.

1. Wybierz pozycję **Usuń grupę zasobów**, wpisz w polu tekstowym pozycję Moja **zasobów** , a następnie wybierz pozycję **Usuń**.

<hr/> 

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację platformy .NET Core i połączyć ją z SQL Database:

- [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji [platformy .NET Core](/aspnet/core/) w systemie <abbr title="App Service w systemie Linux oferuje wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux.">Usługa App Service w systemie Linux</abbr>. Aplikację możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie kodu platformy .NET Core w aplikacji.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

- **Pobierz konto platformy Azure** z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- **Zainstaluj** najnowszą wersję zestawu <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">SDK platformy .NET Core 3,1</a> lub <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">zestawu .NET 5,0 SDK</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Zainstaluj najnowszy interfejs wiersza polecenia platformy Azure</a>**.

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Utwórz aplikację lokalnie

1. Uruchom, `mkdir hellodotnetcore` Aby utworzyć katalog.

    ```bash
    mkdir hellodotnetcore
    ```

1. Uruchom `cd hellodotnetcore` , aby zmienić katalog. 

    ```bash
    cd hellodotnetcore
    ```

1. Uruchom `dotnet new web` , aby utworzyć nową aplikację platformy .NET Core.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Uruchom aplikację lokalnie

1. Uruchom `dotnet run` , aby zobaczyć, jak wygląda po wdrożeniu na platformie Azure.

    ```bash
    dotnet run
    ```
    
1. **Otwórz przeglądarkę internetową** i przejdź do aplikacji w lokalizacji `http://localhost:5000` .

![Test przy użyciu przeglądarki](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Zaloguj się do platformy Azure

Uruchom `az login` , aby zalogować się do platformy Azure.

```azurecli
az login
```

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. wdrażanie aplikacji

1. **Uruchom** `az webapp up` w folderze lokalnym. **Zastąp** <App-Name> globalnie unikatową nazwą.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Rozwiązywanie problemów</summary>
    <ul>
    <li>Jeśli <code>az</code> polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w artykule <a href="#1-prepare-your-environment">Przygotowywanie środowiska</a>.</li>
    <li>Zamień na <code>&lt;app-name&gt;</code> nazwę, która jest unikatowa na całym systemie Azure ( <em> prawidłowe znaki to <code>a-z</code> , <code>0-9</code> i <code>-</code> </em> ). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.</li>
    <li><code>--sku F1</code>Argument tworzy aplikację sieci Web w warstwie cenowej bezpłatna. Pomiń ten argument, aby użyć szybszej warstwy Premium, która wiąże się z godziną.</li>
    <li>Opcjonalnie możesz dołączyć argument, <code>--location &lt;location-name&gt;</code> gdzie <code>&lt;location-name&gt;</code> jest dostępny region platformy Azure. Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> polecenie.</li>
    </ul>
    </details>
    
1. Poczekaj na zakończenie wykonywania polecenia. Może to potrwać kilka minut i kończyć się ciągiem "można uruchomić aplikację w http:// &lt; App-Name &gt; . azurewebsites.NET".

    <details>
    <summary>Co <code>az webapp up</code> robi?</summary>
    <p>Polecenie <code>az webapp up</code> wykonuje następujące akcje:</p>
    <ul>
    <li>Utwórz domyślną grupę zasobów.</li>
    <li>Utwórz domyślny plan App Service.</li>
    <li><a href="/cli/azure/webapp?view=azure-cli-latest#az-webapp-create">Utwórz aplikację App Service</a> o podanej nazwie.</li>
    <li><a href="/azure/app-service/deploy-zip">Spakuj pliki</a> wdrożenia z bieżącego katalogu roboczego do aplikacji.</li>
    <li>W trakcie działania są dostępne komunikaty dotyczące tworzenia zasobów, rejestrowania i wdrażania pliku ZIP.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Przykładowe dane wyjściowe polecenia AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Przykładowe dane wyjściowe polecenia AZ webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Przejdź do aplikacji

**Przejdź do wdrożonej aplikacji** przy użyciu przeglądarki sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. Zaktualizuj i ponownie Wdróż kod

1. **Otwórz plik _Startup.cs_** w katalogu lokalnym. 

1. **Wprowadź niewielką zmianę** w tekście w wywołaniu metody `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Zapisz zmiany**.

1. **Uruchom** `az webapp up` Aby ponownie wdrożyć:

```azurecli
az webapp up --os-type linux
```

<details>
<summary>Co <code>az webapp up</code> to jest ten czas?</summary>
Przy pierwszym uruchomieniu polecenia Zapisano nazwę aplikacji, grupę zasobów i plan App Service w pliku <i>. Azure/config</i> z katalogu głównego projektu. Po ponownym uruchomieniu programu z poziomu katalogu głównego projektu są używane wartości zapisane w <i>. Azure/config</i>, wykryje, że zasoby App Service już istnieją, a następnie ponownie wykonuje wdrożenie zip.
</details>

1. Po zakończeniu wdrażania **kliknij przycisk Odśwież** w otwartym wcześniej oknie przeglądarki.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Zarządzanie nową aplikacją platformy Azure

1. Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Zrzut ekranu przedstawiający stronę App Services z wybraną przykładową aplikacją platformy Azure.":::

1. Na stronie Przegląd można wykonać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)

<hr/> 

## <a name="9-clean-up-resources"></a>9. Czyszczenie zasobów

**Uruchom** `az group delete --name myResourceGroup` Aby usunąć grupę zasobów.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Następne kroki

- [Samouczek: ASP.NET Core aplikacji z SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
