---
title: Profilowanie ASP.NET Core aplikacji sieci Web systemu Linux Azure z Application Insights Profiler | Microsoft Docs
description: Przegląd koncepcyjny i samouczek krok po kroku dotyczący sposobu korzystania z Application Insights Profiler.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 652d63cbf71f7a998462780a5057ce8269463da4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930387"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profilowanie ASP.NET Core aplikacji sieci Web systemu Linux Azure z Application Insights Profiler

Ta funkcja jest obecnie w wersji zapoznawczej.

Dowiedz się, ile czasu zajmuje każda metoda aktywnej aplikacji sieci Web przy użyciu [Application Insights](./app-insights-overview.md). Application Insights Profiler jest teraz dostępna dla ASP.NET Core aplikacji sieci Web hostowanych w systemie Linux w Azure App Service. Ten przewodnik zawiera instrukcje krok po kroku dotyczące sposobu zbierania śladów profilera dla ASP.NET Core aplikacji sieci Web systemu Linux.

Po zakończeniu tego instruktażu aplikacja będzie mogła zbierać ślady profilera, takie jak ślady, które są wyświetlane na obrazie. W tym przykładzie ślad profilera wskazuje, że określone żądanie sieci Web jest powolne z powodu czasu oczekiwania. *Gorąca ścieżka* w kodzie, który spowalnia aplikację, jest oznaczona ikoną płomienia. Metoda **about** w sekcji **HomeController** spowalnia działanie aplikacji sieci Web, ponieważ metoda wywołuje funkcję **Thread. Sleep** .

![Ślady profilera](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Wymagania wstępne
Poniższe instrukcje dotyczą wszystkich środowisk deweloperskich dla systemów Windows, Linux i Mac:

* Zainstaluj [zestaw .NET Core SDK 2.1.2 lub nowszy](https://dotnet.microsoft.com/download/archives).
* Zainstaluj usługę git, postępując zgodnie z instrukcjami w [wprowadzenie — Instalowanie usługi git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurowanie projektu lokalnie

1. Otwórz okno wiersza polecenia na komputerze. Poniższe instrukcje działają w przypadku wszystkich środowisk deweloperskich dla systemów Windows, Linux i Mac.

1. Utwórz aplikację sieci Web ASP.NET Core MVC:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Zmień katalog roboczy na folder główny projektu.

1. Dodaj pakiet NuGet w celu zebrania śladów profilera:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Włącz Application Insights w Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```

1. Włącz Profiler w Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Dodaj wiersz kodu w sekcji **HomeController.cs** , aby losowo opóźnić kilka sekund:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Zapisz i zatwierdź zmiany w repozytorium lokalnym:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Tworzenie aplikacji sieci Web systemu Linux na potrzeby hostowania projektu

1. Utwórz środowisko aplikacji sieci Web przy użyciu App Service w systemie Linux:

    ![Tworzenie aplikacji sieci Web systemu Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Utwórz poświadczenia wdrożenia:

    > [!NOTE]
    > Zapisz hasło, aby użyć go później podczas wdrażania aplikacji sieci Web.

    ![Tworzenie poświadczeń wdrożenia](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Wybierz opcje wdrażania. Skonfiguruj lokalne repozytorium Git w aplikacji sieci Web, postępując zgodnie z instrukcjami na Azure Portal. Repozytorium git jest tworzone automatycznie.

    ![Konfigurowanie repozytorium git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Więcej opcji wdrażania można znaleźć w [dokumentacji App Service](https://docs.microsoft.com/azure/app-service).

## <a name="deploy-your-project"></a>Wdrażanie projektu

1. W oknie wiersza polecenia przejdź do folderu głównego projektu. Dodaj zdalne repozytorium git, aby wskazywało repozytorium na App Service:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Użyj **nazwy użytkownika** użytej do utworzenia poświadczeń wdrożenia.
    * Użyj **nazwy aplikacji** , która została użyta do utworzenia aplikacji sieci Web przy użyciu App Service w systemie Linux.

2. Wdróż projekt, wypychając zmiany do platformy Azure:

    ```console
    git push azure master
    ```

    Powinny zostać wyświetlone dane wyjściowe podobne do następującego przykładu:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Dodawanie Application Insights do monitorowania aplikacji sieci Web

1. [Utwórz zasób Application Insights](./create-new-resource.md).

2. Skopiuj wartość **iKey** zasobu Application Insights i ustaw następujące ustawienia w aplikacjach sieci Web:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Po zmianie ustawień aplikacji lokacja zostanie automatycznie uruchomiona ponownie. Po zastosowaniu nowych ustawień Profiler natychmiast działa przez dwie minuty. Profiler jest uruchamiany przez dwie minuty co godzinę.

3. Wygeneruj ruch dla swojej witryny sieci Web. Ruch można generować przez odświeżenie strony **o** kilka razy.

4. Zaczekaj od dwóch do pięciu minut, aż zdarzenia mają być agregowane do Application Insights.

5. Przejdź do okienka **wydajność** Application Insights w Azure Portal. Ślady profilera można wyświetlić w prawym dolnym rogu okienka.

    ![Wyświetlanie śladów profilera](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Następne kroki
Jeśli używasz kontenerów niestandardowych hostowanych przez Azure App Service, postępuj zgodnie z instrukcjami w temacie [ włączanie Service Profiler dla aplikacji ASP.NET Core kontenerowych](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) , aby włączyć Application Insights Profiler.

Zgłoś wszelkie problemy lub sugestie dotyczące Application Insights repozytorium GitHub: [ApplicationInsights-Profiler-AspNetCore: problemy](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).

