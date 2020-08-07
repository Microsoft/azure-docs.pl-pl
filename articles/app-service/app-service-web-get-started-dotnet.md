---
title: 'Szybki Start: Tworzenie aplikacji ASP.NET Core w języku C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service, wdrażając domyślny szablon aplikacji sieci Web w języku C# ASP.NET Core z poziomu programu Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 77ff1e907b15b129ef03d7ce799631d6d0a9671d
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986189"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Szybki Start: Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure

W tym przewodniku szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą aplikację sieci Web ASP.NET Core w [Azure App Service](overview.md). 

Po zakończeniu będziesz mieć grupę zasobów platformy Azure składającą się z planu hostingu App Service i App Service ze wdrożoną aplikacją sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- W tym przewodniku szybki start wdrożono aplikację do App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](./containers/quickstart-dotnetcore.md).
- Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> przy użyciu obciążeń **ASP.NET i Web Development** .

  Jeśli zainstalowano już program Visual Studio 2019:

  - Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**.
  - Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.


## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Utwórz ASP.NET Core aplikację sieci Web w programie Visual Studio, wykonując następujące czynności:

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W obszarze **Utwórz nowy projekt**wybierz pozycję **ASP.NET Core aplikacja sieci Web** i upewnij się, że w wybranym językach znajduje się język **C#** , a następnie wybierz pozycję **dalej**.

1. W obszarze **Konfigurowanie nowego projektu**Nadaj nazwę projektowi aplikacji sieci Web *myFirstAzureWebApp*, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Możesz wdrożyć dowolny typ ASP.NET Core aplikacji sieci Web na platformie Azure, ale w tym przewodniku Szybki Start wybierz szablon **aplikacja sieci Web** . Upewnij się, że **uwierzytelnianie** jest ustawione na wartość **bez uwierzytelniania**i że żadna inna opcja nie jest zaznaczona. Następnie wybierz przycisk **Utwórz**.

   ![Tworzenie nowej aplikacji sieci Web ASP.NET Core](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację sieci Web lokalnie.

   ![Aplikacja internetowa uruchomiona lokalnie](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web

Aby opublikować aplikację internetową, musisz najpierw utworzyć i skonfigurować nową App Service, do której będzie można opublikować aplikację. 

W ramach konfigurowania App Service utworzysz:

- Nowa [Grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) zawierająca wszystkie zasoby platformy Azure dla usługi.
- Nowy [Plan hostingu](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) , który określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która hostuje aplikację.

Wykonaj następujące kroki, aby utworzyć App Service i opublikować aplikację sieci Web:

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**. Jeśli jeszcze nie zalogowano się na koncie platformy Azure z poziomu programu Visual Studio, wybierz opcję **Dodaj konto** lub **Zaloguj się**. Możesz również utworzyć bezpłatne konto platformy Azure.

1. W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz pozycję **App Service**, wybierz pozycję **Utwórz nowy**, a następnie wybierz pozycję **Utwórz profil**.

   ![Wybieranie miejsca docelowego publikacji](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

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
   
   ![Utwórz nowy plan hostingu](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Pozostaw **Application Insights** wartość *none*.

1. W oknie dialogowym **App Service: Utwórz nowy** wybierz pozycję **Utwórz** , aby rozpocząć tworzenie zasobów platformy Azure.

   ![Utwórz nową usługę App Service](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Po zakończeniu działania kreatora wybierz pozycję **Publikuj**.

   ![Publikowanie aplikacji sieci Web na platformie Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Program Visual Studio publikuje aplikację sieci Web ASP.NET Core na platformie Azure i uruchamia aplikację w domyślnej przeglądarce. 

   ![Opublikowana aplikacja sieci Web ASP.NET uruchomiona na platformie Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

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

   ![Publikowanie aktualizacji w aplikacji sieci Web](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana aplikacja internetowa ASP.NET działająca na platformie Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby zarządzać swoją aplikacją internetową, przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

![Wybierz App Services](./media/app-service-web-get-started-dotnet/app-services.png)

Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-dotnet/select-app-service.png)

Strona **Przegląd** dla aplikacji sieci Web zawiera opcje podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Menu po lewej stronie zawiera dalsze strony umożliwiające skonfigurowanie aplikacji.

![App Service w Azure Portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto programu Visual Studio do utworzenia i wdrożenia aplikacji sieci Web ASP.NET Core w Azure App Service.

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację platformy .NET Core i połączyć ją z SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](app-service-web-tutorial-dotnetcore-sqldb.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
