---
title: 'Szybki start: tworzenie aplikacji C# ASP.NET Core'
description: Dowiedz się, jak uruchamiać aplikacje internetowe w Azure App Service, wdrażając swoją pierwszą ASP.NET podstawową.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff86bedf47395b50dc25e552b8b3ed4176e23b65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769107"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Szybki start: tworzenie aplikacji ASP.NET Core na platformie Azure

::: zone pivot="platform-windows"  

W tym przewodniku Szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację internetową ASP.NET Core w <abbr title="Oparta na protokołu HTTP usługa do hostowania aplikacji internetowych, interfejsów API REST i aplikacji mobilnych.">Azure App Service</abbr>. App Service obsługuje aplikacje .NET 5.0.

Po zakończeniu będziesz mieć platformę Azure <abbr title="Kontener logiczny dla powiązanych zasobów platformy Azure, które można zarządzać jako jednostką.">grupa zasobów</abbr>, składające się z <abbr title="Plan określający lokalizację, rozmiar i funkcje farmy serwerów internetowych, która hostuje aplikację.">Plan usługi App Service</abbr> i an <abbr title="Reprezentacja aplikacji internetowej, która zawiera kod aplikacji, nazwy hostów DNS, certyfikaty i powiązane zasoby.">Aplikacja usługi App Service</abbr> z wdrożoną przykładową ASP.NET Core.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

- **Uzyskiwanie konta platformy Azure przy** użyciu aktywnego konta <abbr title="Podstawowa struktura organizacyjna, w której zarządzasz zasobami na platformie Azure, zwykle skojarzona z jednostką lub działem w organizacji.">subskrypcja</abbr>. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/dotnet/).
- **Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 r.,</a>** ASP.NET tworzenie aplikacji internetowych i tworzenie aplikacji **internetowych.**

<details>
<summary>Masz już Visual Studio 2019 r.?</summary>
Jeśli zainstalowano już program Visual Studio 2019:

<ul>
<li><strong>Zainstaluj najnowsze aktualizacje w programie</strong> Visual Studio wybierając <strong>pozycję Help</strong> Check for &gt; <strong>Updates (Sprawdzanie pomocy dotyczące aktualizacji).</strong> Najnowsze aktualizacje zawierają zestaw SDK platformy .NET 5.0.</li>
<li><strong>Dodaj obciążenie,</strong> wybierając pozycję <strong>Narzędzia Pobierz</strong> narzędzia &gt; <strong>i funkcje.</strong></li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Tworzenie aplikacji ASP.NET Core

1. Otwórz Visual Studio i wybierz **pozycję Utwórz nowy projekt.**

1. W **witrynie Create a new project**(Tworzenie nowego projektu) wybierz pozycję ASP.NET Core Web Application **(Podstawowa** aplikacja internetowa) i upewnij się, że język **C#** jest wymieniony w językach dla tego wyboru, a następnie wybierz pozycję **Next (Dalej).**

1. W **witrynie Configure your new project (Konfigurowanie** nowego projektu) nadaj projektowi aplikacji internetowej *nazwę myFirstAzureWebApp* i wybierz pozycję **Create (Utwórz).**

   ![Konfigurowanie projektu aplikacji internetowej](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. W przypadku aplikacji .NET 5.0 wybierz **pozycję ASP.NET Core 5.0** na liście rozwijanej. W przeciwnym razie użyj wartości domyślnej.

1. Na platformie Azure możesz wdrożyć dowolny typ aplikacji ASP.NET Core, ale w tym przewodniku Szybki start wybierz szablon **ASP.NET Core Web App.** Upewnij **się, że** opcja Uwierzytelnianie jest **ustawiona** na wartość Bez uwierzytelniania i że żadna inna opcja nie jest zaznaczona. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie nowej aplikacji internetowej ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. W Visual Studio menu wybierz pozycję **Rozpocznij debugowanie** bez debugowania,  >   aby uruchomić aplikację internetową lokalnie.

   ![Aplikacja internetowa uruchomiona lokalnie](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. Publikowanie aplikacji internetowej

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj.** 

1. W **witrynie Publish**(Publikuj) wybierz **pozycję Azure** i kliknij przycisk **Next (Dalej).**

1. Twoje opcje zależą od tego, czy już zalogowano się na platformie Azure i czy masz konto Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto lub** Zaloguj **się,** aby zalogować się do subskrypcji platformy Azure. Jeśli już się zalogowano, wybierz odpowiednie konto.

   ![Logowanie do platformy Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Po prawej stronie **App Service kliknij** pozycję **+** .

   ![Nowa App Service aplikacji](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. W **przypadku** subskrypcji zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.

1. W **przypadku grupy zasobów** wybierz pozycję **Nowa.** W **nazwa nowej grupy zasobów,** wprowadź *myResourceGroup* i wybierz **OK.** 

1. W **przypadku opcji Plan hostingu** wybierz pozycję **Nowy.** 

1. W **oknie dialogowym Plan hostingu:** Tworzenie nowego wprowadź wartości określone w poniższej tabeli:

   | Ustawienie  | Sugerowana wartość |
   | -------- | --------------- |
   | **Plan hostingu**  | *myFirstAzureWebAppPlan* |
   | **Lokalizacja**      | *West Europe* |
   | **Rozmiar**          | *Bezpłatna* |
   
   ![Tworzenie nowego planu hostingu](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. W **nazwa**, wprowadź unikatową nazwę aplikacji.

    <details>
        <summary>Których znaków mogę użyć?</summary>
        Prawidłowe znaki to a–z, A–Z, 0–9 i -. Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , gdzie to nazwa <code>&lt;app-name&gt;</code> aplikacji.
    </details>

1. Wybierz **pozycję Utwórz,** aby utworzyć zasoby platformy Azure. 

   ![Tworzenie zasobów aplikacji](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Poczekaj, aż kreator zakończy tworzenie zasobów platformy Azure. Wybierz **pozycję Zakończ,** aby zamknąć kreatora.

1. Na stronie **Publikowanie** kliknij pozycję **Publikuj,** aby wdrożyć projekt. 

    <details>
        <summary>Co Visual Studio zrobić?</summary>
        Visual Studio tworzy, pakuje i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.
    </details>

   ![Opublikowana ASP.NET internetowa uruchomiona na platformie Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. Aktualizowanie aplikacji i ponowne jej wdychaj

1. W **Eksplorator rozwiązań** w projekcie otwórz program **Pages**  >  **Index.cshtml.**

1. Zastąp cały `<div>` tag następującym kodem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

1. Na stronie **Podsumowanie** publikowania wybierz pozycję **Opublikuj**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

    ![Zaktualizowano ASP.NET internetową na platformie Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Zarządzanie aplikacją platformy Azure

1. Przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

    ![Wybierz App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. Na **App Services** wybierz nazwę aplikacji internetowej.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Zrzut ekranu przedstawiający App Services z wybraną przykładową aplikacją internetową.":::

1. Strona **Przegląd** aplikacji internetowej zawiera opcje podstawowego zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera dodatkowe strony służące do konfigurowania aplikacji.

    ![App Service w Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Czyszczenie zasobów

1. W menu Azure Portal lub **na stronie głównej** wybierz pozycję **Grupy zasobów.** Następnie na stronie **Grupy zasobów** wybierz pozycję **myResourceGroup**.

1. Na **stronie myResourceGroup upewnij** się, że wymienione zasoby są zasobami, które chcesz usunąć.

1. Wybierz **pozycję Usuń grupę zasobów,** wpisz **myResourceGroup** w polu tekstowym, aby potwierdzić, a następnie wybierz pozycję **Usuń**.

<hr/> 

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak utworzyć aplikację .NET Core i połączyć ją z SQL Database:

- [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurowanie ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
W tym przewodniku Szybki start pokazano, jak utworzyć [aplikację .NET Core](/aspnet/core/) na stronie <abbr title="App Service dla systemu Linux zapewnia wysoce skalowalną i samonachowalną usługę hostingu w Internecie korzystającą z systemu operacyjnego Linux.">Usługa App Service w systemie Linux</abbr>. Aplikację możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie kodu platformy .NET Core w aplikacji.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

- **Uzyskaj konto platformy Azure z** aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/dotnet/).
- **Zainstaluj** najnowszy zestaw <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">SDK platformy .NET Core 3.1</a> lub <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">zestaw .NET 5.0 SDK.</a>
- **<a href="/cli/azure/install-azure-cli" target="_blank">Zainstaluj najnowszy interfejs wiersza polecenia platformy Azure.</a>**

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Tworzenie aplikacji lokalnie

1. Uruchom `mkdir hellodotnetcore` , aby utworzyć katalog.

    ```bash
    mkdir hellodotnetcore
    ```

1. Uruchom `cd hellodotnetcore` , aby zmienić katalog. 

    ```bash
    cd hellodotnetcore
    ```

1. Uruchom `dotnet new web` , aby utworzyć nową aplikację .NET Core.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Uruchamianie aplikacji lokalnie

1. Uruchom `dotnet run` , aby zobaczyć, jak to wygląda po wdrożeniu na platformie Azure.

    ```bash
    dotnet run
    ```
    
1. **Otwórz przeglądarkę internetową i** przejdź do aplikacji pod adres `http://localhost:5000` .

![Test przy użyciu przeglądarki](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Zaloguj się do platformy Azure

Uruchom, `az login` aby zalogować się do platformy Azure.

```azurecli
az login
```

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. Wdrażanie aplikacji

1. **Uruchom** `az webapp up` w folderze lokalnym. **Zastąp** <nazwę aplikacji> globalnie unikatową nazwą.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Rozwiązywanie problemów</summary>
    <ul>
    <li>Jeśli polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w <code>az</code> <a href="#1-prepare-your-environment">tesłudze Prepare your environment (Przygotowywanie środowiska).</a></li>
    <li>Zastąp <code>&lt;app-name&gt;</code> nazwą unikatową na całej platformie Azure (prawidłowe <em> znaki to , i <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.</li>
    <li>Argument <code>--sku F1</code> tworzy aplikację internetową w warstwie cenowej Bezpłatna. Pomiń ten argument, aby użyć szybszej warstwy Premium, co wiąże się z kosztem godzinowym.</li>
    <li>Opcjonalnie możesz dołączyć argument <code>--location &lt;location-name&gt;</code> , gdzie <code>&lt;location-name&gt;</code> jest dostępnym regionem świadczenia usługi Azure. Listę regionów, w których można zezwolić na korzystanie z konta platformy Azure, możesz pobrać, uruchamiając <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> polecenie .</li>
    </ul>
    </details>
    
1. Poczekaj na ukończenie polecenia. Może to potrwać kilka minut i kończy się na ciągu "Możesz uruchomić aplikację pod nazwą http:// &lt; &gt; .azurewebsites.net".

    <details>
    <summary>Co się <code>az webapp up</code> dzieje?</summary>
    <p>Polecenie <code>az webapp up</code> wykonuje następujące akcje:</p>
    <ul>
    <li>Utwórz domyślną grupę zasobów.</li>
    <li>Utwórz domyślny App Service planu.</li>
    <li><a href="/cli/azure/webapp#az_webapp_create">Utwórz aplikację App Service o</a> określonej nazwie.</li>
    <li><a href="/azure/app-service/deploy-zip">Spakuj pliki</a> wdrożenia z bieżącego katalogu roboczego do aplikacji.</li>
    <li>Podczas działania zapewnia komunikaty dotyczące tworzenia zasobów, rejestrowania i wdrażania pliku ZIP.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Przykładowe dane wyjściowe polecenia az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Przykładowe dane wyjściowe polecenia az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Przejdź do aplikacji

**Przejdź do wdrożonej aplikacji przy** użyciu przeglądarki internetowej.

```bash
http://<app_name>.azurewebsites.net
```

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code&quot;></a>7. Aktualizowanie i ponowne wdychaj kod

1. **Otwórz plik _Startup.cs_** w katalogu lokalnym. 

1. **Wprowadzić niewielką zmianę w** tekście w wywołaniu metody `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync(&quot;Hello Azure!");
    ```
    
1. **Zapisz zmiany.**

1. **Uruchom** `az webapp up` aby ponownie wdeploować:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Co tym <code>az webapp up</code> razem robi?</summary>
    Przy pierwszym użyciu polecenia nazwa aplikacji, grupa zasobów i plan App Service zostały zapisane w pliku <i>.azure/config</i> z katalogu głównego projektu. Po uruchomieniu go ponownie z katalogu głównego projektu program używa wartości zapisanych w pliku <i>.azure/config,</i>wykrywa, że zasoby App Service już istnieją, i ponownie wdraża plik Zip.
    </details>
    
1. Po zakończeniu wdrażania **naciśnij przycisk Odśwież** w oknie przeglądarki, które zostało wcześniej otwarte.

    ![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Zarządzanie nową aplikacją platformy Azure

1. Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Zrzut ekranu przedstawiający App Services z wybraną przykładową aplikacją platformy Azure.":::

1. Strona Przegląd umożliwia wykonywanie podstawowych zadań zarządzania, takich jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

    ![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Czyszczenie zasobów

**Uruchom** `az group delete --name myResourceGroup` , aby usunąć grupę zasobów.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Masz problemy? Daj nam znać.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Następne kroki

- [Samouczek: ASP.NET Core za pomocą SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Konfigurowanie ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
