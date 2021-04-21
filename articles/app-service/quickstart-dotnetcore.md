---
title: 'Szybki start: wdrażanie aplikacji ASP.NET internetowej'
description: Dowiedz się, jak uruchamiać aplikacje internetowe w Azure App Service, wdrażając swoją pierwszą ASP.NET internetową.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762465"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Szybki start: wdrażanie aplikacji ASP.NET internetowej

W tym przewodniku Szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację internetową ASP.NET celu [Azure App Service](overview.md). App Service obsługuje różne wersje aplikacji .NET i zapewnia wysoce skalowalną i samonadajną usługę hostingu w Internecie. ASP.NET internetowe są międzyplatformowe i mogą być hostowane w systemie Linux lub Windows. Po zakończeniu będziesz mieć grupę zasobów platformy Azure składającą się z planu hostingu usługi App Service i App Service z wdrożoną aplikacją internetową.

> [!TIP]
> .NET Core 3.1 to bieżąca wersja long-term support (LTS) programu .NET. Aby uzyskać więcej informacji, zobacz [.NET support policy (Zasady obsługi środowiska .NET).](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

## <a name="prerequisites"></a>Wymagania wstępne

:::zone target="docs" pivot="development-environment-vs"

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019 r. z</a> ASP.NET **tworzenie aplikacji internetowych.**

    Jeśli masz już zainstalowany program Visual Studio 2019:

    - Zainstaluj najnowsze aktualizacje w programie Visual Studio wybierając **pozycję Help** Check for  >  **Updates (Sprawdzanie pomocy dotyczące aktualizacji).**
    - Dodaj obciążenie, wybierając pozycję **Narzędzia Pobierz** narzędzia  >  **i funkcje.**

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Program Visual Studio Code</a>
- Rozszerzenie <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools.</a>

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Zainstaluj najnowszy zestaw .NET Core 3.1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Zainstaluj najnowszy zestaw SDK platformy .NET 5.0. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Zainstaluj pakiet dewelopera .NET Framework 4.8. </a>

> [!NOTE]
> Visual Studio Code jest jednak międzyplatformowe, .NET Framework nie. Jeśli tworzysz aplikacje .NET Framework za pomocą Visual Studio Code, rozważ użycie maszyny z systemem Windows w celu spełnienia zależności kompilacji.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/dotnet)
- Interfejs <a href="/cli/azure/install-azure-cli" target="_blank">wiersza polecenia platformy Azure</a>.
- Zestaw SDK platformy .NET (obejmuje środowisko uruchomieniowe i interfejs wiersza polecenia).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Zainstaluj najnowszy zestaw SDK platformy .NET Core 3.1. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Zainstaluj najnowszy zestaw SDK platformy .NET 5.0. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Zainstaluj najnowszy zestaw SDK platformy .NET 5.0. </a> i <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> pakiet .NET Framework 4.8 Developer Pack. </a>

> [!NOTE]
> Interfejs [wiersza polecenia platformy .NET](/dotnet/core/tools) jest jednak międzyplatformowy, .NET Framework nie. Jeśli tworzysz aplikacje .NET Framework za pomocą interfejsu wiersza polecenia .NET, rozważ użycie maszyny z systemem Windows w celu spełnienia zależności kompilacji.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Otwórz Visual Studio a następnie wybierz **pozycję Utwórz nowy projekt.**
1. W **witrynie Create a new project (Tworzenie** nowego projektu) znajdź i wybierz **pozycję ASP.NET Web Core App**(Podstawowa aplikacja internetowa), a następnie wybierz pozycję Next **(Dalej).**
1. W **witrynie Configure your new project (Konfigurowanie** nowego projektu) nadaj aplikacji _nazwę MyFirstAzureWebApp,_ a następnie wybierz pozycję **Next (Dalej).**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Konfigurowanie ASP.NET core 3.1" border="true":::

1. Wybierz **pozycję .NET Core 3.1 (obsługa długoterminowa).**
1. Upewnij **się, że dla ustawienia Typ uwierzytelniania** ustawiono wartość **Brak.** Wybierz przycisk **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio — wybierz pozycję .NET Core 3.1 i pozycję Brak dla opcji Typ uwierzytelniania." border="true":::

1. W Visual Studio menu wybierz pozycję **Rozpocznij debugowanie** bez debugowania,  >   aby uruchomić aplikację internetową lokalnie.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio — przeglądanie lokalnie przez program .NET Core 3.1" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Otwórz Visual Studio a następnie wybierz **pozycję Utwórz nowy projekt.**
1. W **witrynie Create a new project (Tworzenie** nowego projektu) znajdź i wybierz **pozycję ASP.NET Web Core App (Podstawowa** aplikacja internetowa), a następnie wybierz pozycję Next **(Dalej).**
1. W **witrynie Configure your new project (Konfigurowanie** nowego projektu) nadaj aplikacji _nazwę MyFirstAzureWebApp,_ a następnie wybierz pozycję **Next (Dalej).**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio — skonfiguruj ASP.NET 5.0." border="true":::

1. Wybierz **pozycję .NET Core 5.0 (bieżąca).**
1. Upewnij się, **że dla ustawienia Typ** uwierzytelniania ustawiono wartość **Brak.** Wybierz przycisk **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio — dodatkowe informacje podczas wybierania programu .NET Core 5.0." border="true":::

1. Z menu Visual Studio pozycję Rozpocznij **debugowanie** bez debugowania,  >   aby uruchomić aplikację internetową lokalnie.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio — ASP.NET Core 5.0 działa lokalnie." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

1. Otwórz Visual Studio a następnie wybierz **pozycję Utwórz nowy projekt.**
1. W **witrynie Create a new project**(Tworzenie nowego projektu) znajdź i wybierz pozycję ASP.NET Web Application **(.NET Framework),** a następnie wybierz pozycję **Next (Dalej).**
1. W **witrynie Configure your new project (Konfigurowanie** nowego projektu) nadaj aplikacji _nazwę MyFirstAzureWebApp,_ a następnie wybierz pozycję **Create (Utwórz).**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio — konfigurowanie ASP.NET internetowej w programie Framework 4.8." border="true":::

1. Wybierz szablon **MVC.**
1. Upewnij się, **że dla** ustawienia Authentication (Uwierzytelnianie) **ustawiono wartość No Authentication (Bez uwierzytelniania).** Wybierz przycisk **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio — wybierz szablon MVC." border="true":::

1. Z menu Visual Studio pozycję Rozpocznij **debugowanie** bez debugowania,  >   aby uruchomić aplikację internetową lokalnie.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio — ASP.NET Framework 4.8 działa lokalnie." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Utwórz nowy folder o nazwie _MyFirstAzureWebApp_ i otwórz go w Visual Studio Code. Otwórz okno <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminal</a> i utwórz nową aplikację internetową .NET przy użyciu [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) polecenia .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> Flaga jest bezpłatną zamianą tekstu monikera struktury docelowej (TFM) dla projektu i nie gwarantuje, że szablon obsługujący istnieje lub `--target-framework-override` zostanie skompilowany.  Aplikacje można kompilować i uruchamiać .NET Framework w systemie Windows.

---

W **terminalu** Visual Studio Code uruchom aplikację lokalnie przy użyciu [`dotnet run`](/dotnet/core/tools/dotnet-run) polecenia .

```dotnetcli
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stronie zostanie wyświetlony szablon ASP.NET Core 3.1.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code — uruchom lokalnie program .NET Core 3.1 w przeglądarce." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stronie zostanie wyświetlony szablon ASP.NET Core 5.0.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code — uruchom lokalnie program .NET 5.0 w przeglądarce." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlony szablon ASP.NET Framework 4.8.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code — uruchom lokalnie program .NET 4.8 w przeglądarce." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Otwórz okno terminalu na maszynie, aby otworzyć katalog roboczy. Utwórz nową aplikację internetową .NET przy użyciu polecenia , a następnie zmień [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) katalogi na nowo utworzoną aplikację.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> Flaga jest bezpłatną zamianą tekstu monikera struktury docelowej (TFM) dla projektu i nie gwarantuje, że szablon obsługujący istnieje lub `--target-framework-override` zostanie skompilowany.  Aplikacje można tworzyć tylko .NET Framework w systemie Windows.

---

Z tej samej sesji terminalowej uruchom aplikację lokalnie przy użyciu [`dotnet run`](/dotnet/core/tools/dotnet-run) polecenia .

```dotnetcli
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stronie zostanie wyświetlony szablon ASP.NET Core 3.1.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code — ASP.NET Core 3.1 w przeglądarce lokalnej." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stronie zostanie wyświetlony szablon ASP.NET Core 5.0.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code — ASP.NET Core 5.0 w przeglądarce lokalnej." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlony szablon ASP.NET Framework 4.8.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code — ASP.NET Framework 4.8 w przeglądarce lokalnej." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publikowanie aplikacji internetowej

Aby opublikować aplikację internetową, należy najpierw utworzyć i skonfigurować nową App Service, w których można opublikować aplikację.

W ramach konfigurowania App Service utworzysz:

- Nowa grupa [zasobów,](../azure-resource-manager/management/overview.md#terminology) która będzie zawierać wszystkie zasoby platformy Azure dla usługi.
- Nowy [plan hostingu](overview-hosting-plans.md) określający lokalizację, rozmiar i funkcje farmy serwerów internetowych, która hostuje aplikację.

Wykonaj następujące kroki, aby utworzyć App Service i opublikować aplikację internetową:

:::zone target="docs" pivot="development-environment-vs"

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **MyFirstAzureWebApp** i wybierz polecenie **Publikuj.**
1. W **witrynie Publikuj** wybierz **pozycję Azure,** a następnie **pozycję Dalej.**

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio — publikowanie aplikacji internetowej i docelowej platformy Azure." border="true":::

1. Opcje zależą od tego, czy już zalogowano się na platformie Azure i czy masz konto Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto lub** Zaloguj **się,** aby zalogować się do subskrypcji platformy Azure. Jeśli już się zalogowano, wybierz odpowiednie konto.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio — wybierz okno dialogowe Zaloguj się do platformy Azure.":::

1. Wybierz wartość **określonej wartości docelowej**, **Azure App Service (Linux)** **lub Azure App Service (Windows).**

    > [!IMPORTANT]
    > W przypadku ASP.NET framework 4.8 użyjemy programu **Azure App Service (Windows).**

1. Po prawej stronie **App Service wystąpienia** wybierz pozycję **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio — okno dialogowe App Service nowej aplikacji.":::

1. W **przypadku** subskrypcji zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.
1. W **przypadku grupy zasobów** wybierz pozycję **Nowa.** W **nazwa nowej grupy zasobów**, wprowadź *myResourceGroup* i wybierz **ok**.
1. W **przypadku opcji Plan hostingu** wybierz pozycję **Nowy.**
1. W **oknie dialogowym Plan hostingu: Tworzenie** nowego wprowadź wartości określone w poniższej tabeli:

    | Ustawienie          | Sugerowana wartość          | Opis                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Plan hostingu** | *MyFirstAzureWebAppPlan* | Nazwa planu usługi App Service.                                         |
    | **Lokalizacja**     | *West Europe*            | Centrum danych, w którym hostowana jest aplikacja internetowa.                           |
    | **Rozmiar**         | *Bezpłatna*                   | [Warstwa cenowa][app-service-pricing-tier] określa funkcje hostowania. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Tworzenie nowego planu hostingu":::

1. W **nazwa**, wprowadź unikatową nazwę aplikacji, która zawiera tylko prawidłowe `a-z` znaki, to , , i `A-Z` `0-9` `-` . Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app-name>.azurewebsites.net`, gdzie `<app-name>` to nazwa aplikacji.
1. Wybierz **pozycję Utwórz,** aby utworzyć zasoby platformy Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio — okno dialogowe Tworzenie zasobów aplikacji.":::

   Po zakończeniu pracy kreatora zasoby platformy Azure zostaną utworzone i wszystko będzie gotowe do opublikowania.

1. Wybierz **pozycję Zakończ,** aby zamknąć kreatora.
1. Na stronie **Publikowanie** wybierz pozycję **Opublikuj.** Visual Studio tworzy, pakuje i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona ASP.NET Core 3.1.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio — ASP.NET Core 3.1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona ASP.NET Core 5.0.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio — ASP.NET Core 5.0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona aplikacja ASP.NET Framework 4.8.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio — ASP.NET framework 4.8 na platformie Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Aby wdrożyć aplikację internetową przy użyciu Visual Studio Azure Tools:

1. W Visual Studio Code, otwórz [**paletę**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)poleceń , <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>.
1. Wyszukaj i wybierz pozycję "Azure App Service: Wdrażanie w aplikacji internetowej".
1. Odpowiedz na monity w następujący sposób:

    - Wybierz *folder MyFirstAzureWebApp* jako folder do wdrożenia.
    - Po **wyświetleniu monitu wybierz** pozycję Dodaj konfigurację.
    - Jeśli zostanie wyświetlony monit, zaloguj się do istniejącego konta platformy Azure.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code — zaloguj się do platformy Azure." border="true":::

    - Wybierz **subskrypcję**.
    - Wybierz **pozycję Utwórz nową aplikację internetową... Zaawansowane .**
    - W **przypadku pola Wprowadź globalnie unikatową nazwę** użyj nazwy, która jest unikatowa na całej platformie Azure (prawidłowe znaki to ,*`a-z` `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
    - Wybierz **pozycję Utwórz nową grupę zasobów** i podaj nazwę, na przykład `myResourceGroup` .
    - Po wyświetleniu **monitu o wybranie stosu środowiska uruchomieniowego:**
      - W *przypadku programu .NET Core 3.1* wybierz **pozycję .NET Core 3.1 (LTS)**
      - W *przypadku .NET 5.0* wybierz pozycję **.NET 5.**
      - W *.NET Framework 4.8* wybierz pozycję **ASP.NET V4.8**
    - Wybierz system operacyjny (Windows lub Linux).
        - W *.NET Framework 4.8* niejawnie zostanie wybrany system Windows.
    - Wybierz **pozycję Utwórz nowy plan App Service,** podaj nazwę i wybierz warstwę **cenową Bezpłatna F1.** [][app-service-pricing-tier]
    - Wybierz **pozycję Pomiń** teraz dla Application Insights zasobów.
    - Wybierz lokalizację w pobliżu siebie.

1. Po zakończeniu publikowania wybierz **pozycję Przeglądaj witrynę internetową** w powiadomieniu, a następnie wybierz pozycję Otwórz **po** wyświetleniu monitu.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona ASP.NET Core 3.1.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code — ASP.NET Core 3.1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona ASP.NET Core 5.0.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code — ASP.NET Core 5.0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona aplikacja ASP.NET Framework 4.8.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code — ASP.NET framework 4.8 na platformie Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Wd wdrażaj kod w lokalnym *katalogu MyFirstAzureWebApp* za pomocą [`az webapp up`](/cli/azure/webapp#az_webapp_up) polecenia :

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Jeśli polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy `az` Azure zgodnie z opisem w [tesłudze Prerequisites (Wymagania wstępne).](#prerequisites)
- Zastąp `<app-name>` nazwą unikatową na całej platformie Azure (prawidłowe *znaki to , `a-z` `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- Argument `--sku F1` tworzy aplikację internetową w warstwie **cenowej** [Bezpłatna.][app-service-pricing-tier] Pomiń ten argument, aby użyć szybszej warstwy Premium, co wiąże się z kosztem godzinowym.
- `<os>`Zamień na `linux` lub `windows` . Należy użyć podczas `windows` określania wartości docelowej *ASP.NET Framework 4.8.*
- Opcjonalnie możesz dołączyć argument `--location <location-name>` , gdzie `<location-name>` jest dostępnym regionem świadczenia usługi Azure. Listę regionów, w których można zezwolić na korzystanie z konta platformy Azure, możesz pobrać, uruchamiając [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) polecenie .

Ukończenie polecenia może potrwać kilka minut. Podczas działania program udostępnia komunikaty dotyczące tworzenia grupy zasobów, planu App Service i hostowania aplikacji, konfigurowania rejestrowania, a następnie wdrażania pliku ZIP. Następnie jest wyświetlany komunikat z adresem URL aplikacji:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Otwórz przeglądarkę internetową i przejdź do adresu URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stronie zostanie wyświetlona ASP.NET Core 3.1.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Interfejs wiersza polecenia — ASP.NET Core 3.1 na platformie Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stronie zostanie wyświetlona ASP.NET Core 5.0.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Interfejs wiersza polecenia — ASP.NET Core 5.0 na platformie Azure.":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlona ASP.NET framework 4.8.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="Interfejs wiersza polecenia — ASP.NET platformy Azure w wersji 4.8.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Wykonaj następujące kroki, aby zaktualizować i ponownie wdychać aplikację internetową:

:::zone target="docs" pivot="development-environment-vs"

1. W **Eksplorator rozwiązań** w projekcie otwórz pliku *Index.cshtml.*
1. Zastąp pierwszy `<div>` element następującym kodem:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Zapisz zmiany.

1. Aby ponownie wdać się na platformę Azure, kliknij prawym przyciskiem myszy projekt **MyFirstAzureWebApp** w witrynie **Eksplorator rozwiązań** wybierz pozycję **Opublikuj.**
1. Na stronie **Podsumowanie** publikowania wybierz pozycję **Opublikuj**.

    Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja ASP.NET Core 3.1.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio — zaktualizowano ASP.NET Core 3.1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona zaktualizowana ASP.NET Core 5.0.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio — zaktualizowano ASP.NET Core 5.0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja ASP.NET Framework 4.8.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio — zaktualizowano aplikację ASP.NET Framework 4.8 na platformie Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Otwórz plik *Index.cshtml*.
1. Zastąp pierwszy `<div>` element następującym kodem:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Zapisz zmiany.

1. Otwórz pasek Visual Studio Code **,** wybierz **ikonę platformy Azure,** aby rozwinąć jej opcje.
1. W **węźle APP SERVICE** rozwiń swoją subskrypcję i kliknij prawym przyciskiem myszy pozycję **MyFirstAzureWebApp.**
1. Wybierz pozycję **Deploy to Web App... (Wd wdrażaj do aplikacji internetowej...**).
1. Po **wyświetleniu** monitu wybierz pozycję Wd wdrażaj.
1. Po zakończeniu publikowania wybierz **pozycję Przeglądaj witrynę internetową** w powiadomieniu, a następnie wybierz pozycję Otwórz **po** wyświetleniu monitu.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona zaktualizowana ASP.NET Core 3.1.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code — zaktualizowano ASP.NET Core 3.1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona zaktualizowana ASP.NET Core 5.0.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code — zaktualizowano ASP.NET Core 5.0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja ASP.NET Framework 4.8.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code — zaktualizowano aplikację ASP.NET Framework 4.8 na platformie Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

W katalogu lokalnym otwórz plik *Index.cshtml.* Zastąp pierwszy `<div>` element:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Zapisz zmiany, a następnie ponownie wdeń aplikację przy użyciu `az webapp up` polecenia :

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 jest międzyplatformowy, na podstawie poprzedniego wdrożenia zastąp `<os>` element albo `linux` lub `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 jest międzyplatformowy, na podstawie poprzedniego wdrożenia zastąp `<os>` element albo `linux` lub `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 ma zależności struktury i musi być hostowany w systemie Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Jeśli interesuje Cię hostowanie aplikacji .NET w systemie Linux, rozważ migrowanie z programu [ASP.NET Framework do programu ASP.NET Core.](/aspnet/core/migration/proper-to-2x)

---

To polecenie używa wartości buforowanych lokalnie w pliku *.azure/config,* w tym nazwy aplikacji, grupy zasobów i App Service planu.

Po zakończeniu wdrażania wróć do okna przeglądarki otwartego w kroku **Przejdź** do aplikacji i naciśnij przycisk Odśwież.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stronie zostanie wyświetlona zaktualizowana ASP.NET Core 3.1.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Interfejs wiersza polecenia — zaktualizowano ASP.NET Core 3.1 na platformie Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stronie zostanie wyświetlona zaktualizowana ASP.NET Core 5.0.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Interfejs wiersza polecenia — ASP.NET core 5.0 na platformie Azure.":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlona zaktualizowana aplikacja ASP.NET Framework 4.8.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Interfejs wiersza polecenia — zaktualizowano ASP.NET platformy Azure w wersji 4.8.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby zarządzać aplikacją internetową, przejdź do [witryny Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal — wybierz App Services opcję.":::

Na **App Services** wybierz nazwę aplikacji internetowej.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal — App Services z wybraną przykładową aplikacją internetową.":::

Strona **Przegląd** aplikacji internetowej zawiera opcje podstawowego zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera dodatkowe strony służące do konfigurowania aplikacji.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal — App Service przeglądu.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono i wdrożono ASP.NET internetową w Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

W następnym artykule dowiesz się, jak utworzyć aplikację .NET Core i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core z bazą danych SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie ASP.NET Core 3.1](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

W następnym artykule dowiesz się, jak utworzyć aplikację .NET Core i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core z bazą danych SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie ASP.NET Core 5.0](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

W następnym artykule dowiesz się, jak utworzyć aplikację .NET Framework i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET z bazą danych SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurowanie ASP.NET Framework](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
