---
title: 'Szybki Start: wdrażanie aplikacji sieci Web ASP.NET'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service przez wdrożenie pierwszej aplikacji ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058342"
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

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Szybki Start: wdrażanie aplikacji sieci Web ASP.NET

W tym przewodniku szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację sieci Web ASP.NET do [Azure App Service](overview.md). App Service obsługuje różne wersje aplikacji .NET i zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. Aplikacje sieci Web ASP.NET są na wielu platformach i mogą być hostowane w systemie Linux lub Windows. Po zakończeniu będziesz mieć grupę zasobów platformy Azure składającą się z planu hostingu App Service i App Service ze wdrożoną aplikacją sieci Web.

> [!TIP]
> .NET Core 3,1 to aktualna wersja długoterminowa pomocy technicznej (LTS) platformy .NET. Aby uzyskać więcej informacji, zobacz [zasady pomocy technicznej platformy .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Wymagania wstępne

:::zone target="docs" pivot="development-environment-vs"

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Program Visual Studio 2019</a> z **ASP.NET i programowaniem w sieci Web** .

    Jeśli zainstalowano już program Visual Studio 2019:

    - Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**.
    - Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Program Visual Studio Code</a>
- Rozszerzenie <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">narzędzi platformy Azure</a> .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Zainstaluj najnowszą wersję zestawu SDK platformy .NET Core 3,1. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Zainstaluj najnowszą wersję zestawu .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Zainstaluj pakiet dewelopera .NET Framework 4,8. </a>

> [!NOTE]
> Visual Studio Code jest na wielu platformach, jednak .NET Framework nie jest. Jeśli tworzysz aplikacje .NET Framework przy użyciu Visual Studio Code, rozważ użycie komputera z systemem Windows w celu spełnienia zależności kompilacji.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet).
- <a href="/cli/azure/install-azure-cli" target="_blank">Interfejs wiersza polecenia platformy Azure</a>.
- Zestaw SDK platformy .NET (zawiera środowisko uruchomieniowe i interfejs wiersza polecenia).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Zainstaluj najnowszą wersję zestawu SDK platformy .NET Core 3,1. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Zainstaluj najnowszą wersję zestawu .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Zainstaluj najnowszą wersję zestawu .net 5,0 SDK. </a> i <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Pakiet dewelopera .NET Framework 4,8. </a>

> [!NOTE]
> [Interfejs wiersza polecenia platformy .NET](/dotnet/core/tools) jest na wielu platformach, jednak .NET Framework nie jest. Jeśli tworzysz aplikacje .NET Framework przy użyciu interfejsu wiersza polecenia platformy .NET, rozważ użycie komputera z systemem Windows w celu spełnienia zależności kompilacji.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji internetowej platformy ASP.NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** Znajdź i wybierz pozycję **ASP.NET Web Core App**, a następnie wybierz pozycję **Next (dalej**).
1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę aplikacji _MyFirstAzureWebApp_, a następnie wybierz pozycję **dalej**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Konfigurowanie aplikacji sieci Web ASP.NET Core 3,1" border="true":::

1. Wybierz pozycję **.NET Core 3,1 (obsługa długoterminowa)**.
1. Upewnij się, że **Typ uwierzytelniania** to **none**. Wybierz przycisk **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio — wybierz opcję .NET Core 3,1 i brak dla typu uwierzytelniania." border="true":::

1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio — przeglądanie programu .NET Core 3,1 lokalnie" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** Znajdź i wybierz pozycję **ASP.NET Web Core App**, a następnie wybierz pozycję **Next (dalej**).
1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę aplikacji _MyFirstAzureWebApp_, a następnie wybierz pozycję **dalej**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio — Konfigurowanie aplikacji sieci Web ASP.NET 5,0." border="true":::

1. Wybierz pozycję **.NET Core 5,0 (bieżąca)**.
1. Upewnij się, że **Typ uwierzytelniania** to **none**. Wybierz przycisk **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio — dodatkowe informacje podczas wybierania platformy .NET Core 5,0." border="true":::

1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Program Visual Studio — ASP.NET Core 5,0 działa lokalnie." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** Znajdź i wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)**, a następnie wybierz pozycję **dalej**.
1. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę aplikacji _MyFirstAzureWebApp_, a następnie wybierz pozycję **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio — Konfigurowanie aplikacji sieci Web ASP.NET Framework 4,8." border="true":::

1. Wybierz szablon **MVC** .
1. Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**. Wybierz przycisk **Utwórz**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio — wybierz szablon MVC." border="true":::

1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Program Visual Studio — ASP.NET Framework 4,8 działa lokalnie." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Utwórz nowy folder o nazwie _MyFirstAzureWebApp_ i otwórz go w Visual Studio Code. Otwórz okno <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminalu</a> i Utwórz nową aplikację sieci Web platformy .NET przy użyciu [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) polecenia.

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
> Flaga to niezależna od `--target-framework-override` postaci tekstowa zastępcza monikera platformy docelowej (TFM) dla projektu i *nie gwarantuje* , że szablon pomocniczy istnieje lub kompiluje. Możesz tworzyć i uruchamiać tylko aplikacje .NET Framework w systemie Windows.

---

Z poziomu **terminalu** w Visual Studio Code Uruchom aplikację lokalnie przy użyciu [`dotnet run`](/dotnet/core/tools/dotnet-run) polecenia.

```dotnetcli
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Zobaczysz, że na stronie zostanie wyświetlona aplikacja internetowa szablon ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code — Uruchom program .NET Core 3,1 w przeglądarce lokalnie." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Zobaczysz, że na stronie zostanie wyświetlona aplikacja internetowa szablon ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code — Uruchom platformę .NET 5,0 w przeglądarce lokalnie." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlona aplikacja internetowa szablon ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code — Uruchom platformę .NET 4,8 w przeglądarce lokalnie." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Otwórz okno terminalu na komputerze w katalogu roboczym. Utwórz nową aplikację sieci Web platformy .NET przy użyciu [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) polecenia, a następnie zmień katalogi na nowo utworzoną aplikację.

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
> Flaga to niezależna od `--target-framework-override` postaci tekstowa zastępcza monikera platformy docelowej (TFM) dla projektu i *nie gwarantuje* , że szablon pomocniczy istnieje lub kompiluje. Możesz tworzyć tylko .NET Framework aplikacje w systemie Windows.

---

Z tej samej sesji terminalowej Uruchom aplikację lokalnie przy użyciu [`dotnet run`](/dotnet/core/tools/dotnet-run) polecenia.

```dotnetcli
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Zobaczysz, że na stronie zostanie wyświetlona aplikacja internetowa szablon ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code-ASP.NET Core 3,1 w przeglądarce lokalnej." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Zobaczysz, że na stronie zostanie wyświetlona aplikacja internetowa szablon ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code-ASP.NET Core 5,0 w przeglądarce lokalnej." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlona aplikacja internetowa szablon ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code — ASP.NET Framework 4,8 w lokalnej przeglądarce." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web

Aby opublikować aplikację internetową, musisz najpierw utworzyć i skonfigurować nową App Service, do której będzie można opublikować aplikację.

W ramach konfigurowania App Service utworzysz:

- Nowa [Grupa zasobów](../azure-resource-manager/management/overview.md#terminology) zawierająca wszystkie zasoby platformy Azure dla usługi.
- Nowy [Plan hostingu](overview-hosting-plans.md) , który określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.

Wykonaj następujące kroki, aby utworzyć App Service i opublikować aplikację sieci Web:

:::zone target="docs" pivot="development-environment-vs"

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **MyFirstAzureWebApp** i wybierz polecenie **Publikuj**.
1. W obszarze **Publikowanie** wybierz pozycję **Azure** , a następnie kliknij przycisk **dalej**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio — publikowanie aplikacji sieci Web i docelowej platformy Azure." border="true":::

1. Twoje opcje zależą od tego, czy użytkownik jest już zalogowany na platformie Azure i czy masz konto programu Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto** lub **Zaloguj** się, aby zalogować się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz odpowiednie konto.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio — wybierz pozycję Zaloguj się do okna dialogowego platformy Azure.":::

1. Wybierz **konkretny element docelowy**, albo **Azure App Service (Linux)** lub **Azure App Service (Windows)**.

    > [!IMPORTANT]
    > W przypadku określania wartości docelowej ASP.NET Framework 4,8 będzie używana **Azure App Service (Windows)**.

1. Na prawo od **wystąpień App Service** wybierz pozycję **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio — nowe okno dialogowe aplikacji App Service.":::

1. W obszarze **subskrypcja** Zaakceptuj subskrypcję wymienioną na liście lub wybierz nową z listy rozwijanej.
1. W obszarze **Grupa zasobów** wybierz pozycję **Nowy**. W polu **Nazwa nowej grupy zasobów wprowadź nazwę** *zasobu* , a następnie wybierz **przycisk OK**.
1. W obszarze **Plan hostingu** wybierz pozycję **Nowy**.
1. W obszarze **Plan hostingu: Utwórz nowe** okno dialogowe, wprowadź wartości podane w poniższej tabeli:

    | Ustawienie          | Sugerowana wartość          | Opis                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Plan hostingu** | *MyFirstAzureWebAppPlan* | Nazwa planu usługi App Service.                                         |
    | **Lokalizacja**     | *West Europe*            | Centrum danych, w którym hostowana jest aplikacja internetowa.                           |
    | **Rozmiar**         | *Bezpłatna*                   | [Warstwa cenowa][app-service-pricing-tier] określa funkcje hostowania. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Utwórz nowy plan hostingu":::

1. W polu **Nazwa** wprowadź unikatową nazwę aplikacji, która zawiera tylko prawidłowe znaki to `a-z` , `A-Z` , `0-9` i `-` . Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app-name>.azurewebsites.net`, gdzie `<app-name>` to nazwa aplikacji.
1. Wybierz pozycję **Utwórz** , aby utworzyć zasoby platformy Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio — okno dialogowe Tworzenie zasobów aplikacji.":::

   Po zakończeniu działania kreatora zasoby platformy Azure są tworzone dla Ciebie i wszystko jest gotowe do opublikowania.

1. Wybierz pozycję **Zakończ** , aby zamknąć kreatora.
1. Na stronie **Publikowanie** wybierz pozycję **Publikuj**. Program Visual Studio kompiluje, pakuje i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona aplikacja sieci Web ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Aplikacja internetowa programu Visual Studio — ASP.NET Core 3,1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona aplikacja sieci Web ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Aplikacja internetowa programu Visual Studio — ASP.NET Core 5,0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona aplikacja internetowa ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Aplikacja internetowa programu Visual Studio — ASP.NET Framework 4,8 na platformie Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Aby wdrożyć aplikację sieci Web przy użyciu rozszerzenia Visual Studio Azure Tools:

1. W Visual Studio Code Otwórz [**paletę poleceń**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>Ctrl</kbd> + <kbd>SHIFT</kbd> + <kbd>P</kbd>.
1. Wyszukaj i wybierz pozycję "Azure App Service: Wdróż w aplikacji sieci Web".
1. Odpowiedz na następujące polecenia w następujący sposób:

    - Wybierz pozycję *MyFirstAzureWebApp* jako folder do wdrożenia.
    - Po wyświetleniu monitu wybierz pozycję **Dodaj konfigurację** .
    - Jeśli zostanie wyświetlony monit, zaloguj się do istniejącego konta platformy Azure.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code — Zaloguj się do platformy Azure." border="true":::

    - Wybierz swoją **subskrypcję**.
    - Wybierz pozycję **Utwórz nową aplikację sieci Web... Zaawansowane**.
    - Aby **wprowadzić globalnie unikatową nazwę**, użyj nazwy, która jest unikatowa dla wszystkich platform Azure (*prawidłowe znaki to `a-z` , `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
    - Wybierz pozycję **Utwórz nową grupę zasobów** i podaj nazwę, taką jak `myResourceGroup` .
    - Po wyświetleniu monitu o **wybranie stosu środowiska uruchomieniowego**:
      - W przypadku *platformy .net core 3,1* wybierz pozycję **.NET Core 3,1 (LTS)**
      - W przypadku *platformy .net 5,0* wybierz pozycję **.NET 5**
      - W przypadku *.NET Framework 4,8* wybierz pozycję **ASP.NET v 4.8**
    - Wybierz system operacyjny (Windows lub Linux).
        - W przypadku *.NET Framework 4,8* zostanie wybrany system Windows niejawnie.
    - Wybierz pozycję **Utwórz nowy plan App Service**, podaj nazwę, a następnie wybierz pozycję **F1 bezpłatna** [warstwa cenowa][app-service-pricing-tier].
    - Wybierz pozycję **Pomiń teraz** dla zasobu Application Insights.
    - Wybierz lokalizację w pobliżu siebie.

1. Po zakończeniu publikowania wybierz opcję **Przeglądaj witrynę internetową** w powiadomieniu i wybierz pozycję **Otwórz** po wyświetleniu monitu.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona aplikacja sieci Web ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Aplikacja sieci Web Visual Studio Code-ASP.NET Core 3,1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona aplikacja sieci Web ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Aplikacja sieci Web Visual Studio Code-ASP.NET Core 5,0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona aplikacja internetowa ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Aplikacja internetowa Visual Studio Code ASP.NET Framework 4,8 na platformie Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Wdróż kod w lokalnym katalogu *MyFirstAzureWebApp* przy użyciu [`az webapp up`](/cli/azure/webapp#az_webapp_up) polecenia:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Jeśli `az` polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w sekcji [wymagania wstępne](#prerequisites).
- Zamień na `<app-name>` nazwę, która jest unikatowa na całym systemie Azure (*prawidłowe znaki to `a-z` , `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- `--sku F1`Argument tworzy aplikację sieci Web w [warstwie cenowej][app-service-pricing-tier] **bezpłatna** . Pomiń ten argument, aby użyć szybszej warstwy Premium, która wiąże się z godziną.
- Zamień `<os>` na `linux` lub `windows` . Należy użyć, `windows` Jeśli celem jest *ASP.NET Framework 4,8*.
- Opcjonalnie możesz dołączyć argument, `--location <location-name>` gdzie `<location-name>` jest dostępny region platformy Azure. Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) polecenie.

Wykonanie polecenia może potrwać kilka minut. W trakcie działania są dostępne komunikaty dotyczące tworzenia grupy zasobów, planu App Service i aplikacji hostingu, konfigurowania rejestrowania, a następnie wykonywania wdrożenia ZIP. Następnie generuje komunikat z adresem URL aplikacji:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Otwórz przeglądarkę internetową i przejdź do adresu URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stronie zostanie wyświetlona aplikacja sieci Web ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Aplikacja internetowa CLI-ASP.NET Core 3,1 na platformie Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stronie zostanie wyświetlona aplikacja sieci Web ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Aplikacja internetowa CLI-ASP.NET Core 5,0 na platformie Azure.":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlona aplikacja internetowa ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="Interfejs wiersza polecenia — ASP.NET Framework 4,8 Web App na platformie Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Wykonaj następujące kroki, aby zaktualizować i ponownie wdrożyć aplikację internetową:

:::zone target="docs" pivot="development-environment-vs"

1. W **Eksplorator rozwiązań** w projekcie Otwórz pozycję *index. cshtml*.
1. Zastąp pierwszy `<div>` element następującym kodem:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Zapisz zmiany.

1. Aby ponownie wdrożyć na platformie Azure, kliknij prawym przyciskiem myszy projekt **MyFirstAzureWebApp** w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj**.
1. Na stronie **Publikowanie** podsumowania wybierz pozycję **Publikuj**.

    Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web w ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Program Visual Studio — zaktualizowana aplikacja internetowa ASP.NET Core 3,1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web w ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Program Visual Studio — zaktualizowana aplikacja internetowa ASP.NET Core 5,0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio — zaktualizowana aplikacja internetowa ASP.NET Framework 4,8 na platformie Azure.":::

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

1. Otwórz Visual Studio Code **pasek boczny**, wybierz ikonę **platformy Azure** , aby rozwinąć jej opcje.
1. W węźle **App Service** Rozwiń swoją subskrypcję, a następnie kliknij prawym przyciskiem myszy **MyFirstAzureWebApp**.
1. Wybierz pozycję **Wdróż w aplikacji sieci Web.**...
1. Po wyświetleniu monitu wybierz pozycję **Wdróż** .
1. Po zakończeniu publikowania wybierz opcję **Przeglądaj witrynę internetową** w powiadomieniu i wybierz pozycję **Otwórz** po wyświetleniu monitu.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web w ASP.NET Core 3,1.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code zaktualizowana aplikacja internetowa ASP.NET Core 3,1 na platformie Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web w ASP.NET Core 5,0.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code zaktualizowana aplikacja internetowa ASP.NET Core 5,0 na platformie Azure.":::

    ### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

    Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web ASP.NET Framework 4,8.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code — zaktualizowana aplikacja sieci Web ASP.NET Framework 4,8 na platformie Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

W katalogu lokalnym Otwórz plik *index. cshtml* . Zastąp pierwszy `<div>` element:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Zapisz zmiany, a następnie ponownie Wdróż aplikację przy użyciu `az webapp up` polecenia:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1 to platforma międzyplatformowa oparta na poprzednim wdrożeniu Zastąp `<os>` z `linux` lub `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5,0 to platforma międzyplatformowa oparta na poprzednim wdrożeniu Zastąp `<os>` z `linux` lub `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4,8 ma zależności struktury i muszą być hostowane w systemie Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Jeśli interesuje Cię hosting aplikacji .NET w systemie Linux, rozważ migrację z [ASP.NET Framework do ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

To polecenie używa wartości, które są buforowane lokalnie w pliku *. Azure/config* , łącznie z nazwą aplikacji, grupą zasobów i planem App Service.

Po zakończeniu wdrożenia przejdź z powrotem do okna przeglądarki otwartego w kroku **Przejdź do aplikacji** , a następnie kliknij przycisk Odśwież.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web w ASP.NET Core 3,1.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Interfejs wiersza polecenia — Zaktualizowano aplikację sieci Web ASP.NET Core 3,1 na platformie Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web w ASP.NET Core 5,0.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Interfejs wiersza polecenia — Zaktualizowano aplikację sieci Web ASP.NET Core 5,0 na platformie Azure.":::

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Na stronie zostanie wyświetlona zaktualizowana aplikacja sieci Web ASP.NET Framework 4,8.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Interfejs wiersza polecenia — Zaktualizowano aplikację sieci Web ASP.NET Framework 4,8 na platformie Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby zarządzać swoją aplikacją internetową, przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Witryna Azure Portal — wybierz opcję App Services.":::

Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Witryna Azure Portal — App Services stronie z wybraną przykładową aplikacją sieci Web.":::

Strona **Przegląd** dla aplikacji sieci Web zawiera opcje podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera dalsze strony umożliwiające skonfigurowanie aplikacji.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Witryna Azure Portal — App Service stronie Przegląd.":::

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

W tym przewodniku szybki start utworzono i wdrożono aplikację sieci Web ASP.NET w celu Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację platformy .NET Core i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z usługą SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core 3,1](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację platformy .NET Core i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET Core aplikacji z usługą SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core 5,0](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[ .NET Framework 4.8](#tab/netframework48)

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację .NET Framework i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [Samouczek: aplikacja ASP.NET z bazą danych SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Framework](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
