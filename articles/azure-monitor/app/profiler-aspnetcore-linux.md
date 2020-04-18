---
title: Profil ASP.NET aplikacje internetowe Core Azure Linux za pomocą aplikacji Profiler aplikacji | Dokumenty firmy Microsoft
description: Omówienie koncepcyjne i samouczek krok po kroku na temat korzystania z aplikacji Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d845e245a242a88d16a2597f0144a0ae4a727cb0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640971"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET aplikacje internetowe Core Azure Linux za pomocą aplikacji Profiler aplikacji

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Dowiedz się, ile czasu poświęcają na każdą metodę aplikacji sieci web na żywo podczas korzystania z [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Profiler usługi Application Insights jest teraz dostępny dla aplikacji sieci Web ASP.NET Core, które są hostowane w systemie Linux w usłudze Azure App Service. Ten przewodnik zawiera instrukcje krok po kroku dotyczące sposobu, w jaki można zbierać ślady profilera dla aplikacji sieci web ASP.NET Core Linux.

Po zakończeniu tego przewodnika aplikacja może zbierać ślady profilera, takie jak ślady, które są wyświetlane na obrazie. W tym przykładzie śledzenia profilera wskazuje, że określone żądanie sieci web jest powolny ze względu na czas oczekiwania. *Gorąca ścieżka* w kodzie spowalniającym aplikację jest oznaczona ikoną płomienia. **O** metoda w **HomeController** sekcji spowalnia aplikację sieci web, ponieważ metoda wywołuje **Thread.Sleep** funkcji.

![Ślady profilera](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Wymagania wstępne
Poniższe instrukcje dotyczą wszystkich środowisk programistycznych systemów Windows, Linux i Mac:

* Zainstaluj plik [.NET Core SDK 2.1.2 lub nowszy](https://dotnet.microsoft.com/download/archives).
* Zainstaluj Git, postępując zgodnie z instrukcjami dotyczącymi [Wprowadzenie - Instalowanie Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurowanie projektu lokalnie

1. Otwórz okno wiersza polecenia na komputerze. Poniższe instrukcje działają dla wszystkich środowisk programistów systemu Windows, Linux i Mac.

1. Tworzenie ASP.NET podstawowej aplikacji sieci web MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Zmień katalog roboczy na folder główny projektu.

1. Dodaj pakiet NuGet, aby zebrać ślady profilera:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Włącz usługę Application Insights w Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Włącz profiler w Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Dodaj wiersz kodu w sekcji **HomeController.cs,** aby losowo opóźnić kilka sekund:

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

1. Zapisz i zaobe go zatwierdzić w lokalnym repozytorium:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Tworzenie aplikacji sieci Web systemu Linux do obsługi projektu

1. Tworzenie środowiska aplikacji sieci web przy użyciu usługi App Service w systemie Linux:

    ![Tworzenie aplikacji sieci Web systemu Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Tworzenie poświadczeń wdrożenia:

    > [!NOTE]
    > Nagraj hasło do późniejszego użycia podczas wdrażania aplikacji sieci web.

    ![Tworzenie poświadczeń wdrożenia](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Wybierz opcje wdrażania. Skonfiguruj lokalne repozytorium Git w aplikacji sieci web, postępując zgodnie z instrukcjami w witrynie Azure portal. Repozytorium Git jest tworzone automatycznie.

    ![Konfigurowanie repozytorium Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Aby uzyskać więcej opcji wdrażania, zobacz [ten artykuł](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Wdrażanie projektu

1. W oknie wiersza polecenia przejdź do folderu głównego projektu. Dodaj zdalne repozytorium Git, aby wskazać repozytorium w usłudze App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Użyj **nazwy użytkownika** użytej do utworzenia poświadczeń wdrożenia.
    * Użyj **nazwy aplikacji** użytej do utworzenia aplikacji sieci web przy użyciu usługi App Service w systemie Linux.

2. Wdrażanie projektu przez wypychanie zmian na platformie Azure:

    ```
    git push azure master
    ```

Powinien zostać wyświetlony wynik podobny do następującego przykładu:

    ```
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
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Dodawanie statystyk aplikacji w celu monitorowania aplikacji sieci Web

1. [Tworzenie zasobu usługi Application Insights](./../../azure-monitor/app/create-new-resource.md ).

2. Skopiuj wartość **iKey** zasobu usługi Application Insights i ustaw następujące ustawienia w aplikacjach sieci Web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Po zmianie ustawień aplikacji witryna zostanie automatycznie uruchomiona ponownie. Po zastosowaniu nowych ustawień profiler natychmiast działa przez dwie minuty. Profiler następnie działa przez dwie minuty co godzinę.

3. Wygeneruj ruch w witrynie. Możesz wygenerować ruch, odświeżając witrynę **Informacje o** stronie kilka razy.

4. Poczekaj od dwóch do pięciu minut, aby zdarzenia agregować do usługi Application Insights.

5. Przejdź do okienka **wydajności** usługi Application Insights w witrynie Azure portal. Ślady profilera można wyświetlić w prawym dolnym rogu okienka.

    ![Wyświetl ślady profilera](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Następne kroki
Jeśli używasz kontenerów niestandardowych, które są obsługiwane przez usługę Azure App Service, postępuj zgodnie z instrukcjami w [Włącz profiler usługi dla konteneryzowanej aplikacji ASP.NET Core,](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) aby włączyć profiler usługi Application Insights.

Zgłaszaj wszelkie problemy lub sugestie do repozytorium GitHub usługi Application Insights: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
