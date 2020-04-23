---
title: 'Szybki start: uruchamianie aplikacji Linux ASP.NET Core'
description: Rozpocznij pracę z aplikacjami systemu Linux w usłudze Azure App Service, wdrażając pierwszą aplikację ASP.NET Core w kontenerze systemu Linux w usłudze App Service.
keywords: azure app service, aplikacja internetowa, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 349aa4652ae9f52641b5865e48dcd4c113ca7f9d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086298"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Tworzenie aplikacji platformy ASP.NET Core w usłudze App Service dla systemu Linux

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Linux. Aby wdrożyć aplikację w usłudze App Service w systemie _Windows_, zobacz [Tworzenie aplikacji ASP.NET Core na platformie Azure](../app-service-web-get-started-dotnet.md).
>

[Usługa App Service w systemie Linux](app-service-linux-intro.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingową przy użyciu systemu operacyjnego Linux. Ten przewodnik szybkiego startu pokazuje, jak utworzyć aplikację platformy [.NET Core](https://docs.microsoft.com/aspnet/core/) w usłudze App Service w systemie Linux. Aplikację możesz utworzyć przy użyciu [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), a usługa Git umożliwia wdrażanie kodu platformy .NET Core w aplikacji.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

Kroki podane w tym artykule możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Zainstaluj najnowszy pakiet .NET Core SDK 3.1</a>

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Przejdź do nowo utworzonej aplikacji. Zamień _ &lt;nazwę aplikacji>_ nazwą aplikacji.

```bash
https://<app-name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja:

![Pusta strona aplikacji](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

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

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

Przykładowy kod .NET Core jest uruchamiany w usłudze App Service dla systemu Linux z wbudowanym obrazem.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację platformy .NET Core w usłudze App Service w systemie Linux.

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

Po zakończeniu wdrażania przełącz się z powrotem do okna przeglądarki, które zostało otwarte w kroku **Przeglądaj do aplikacji,** i naciśnij przycisk odświeżania.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-dotnetcore/portal-app-service-list.png)

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. 

![Strona usługi App Service w witrynie Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: ASP.NET podstawowa aplikacja z bazą danych SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)
