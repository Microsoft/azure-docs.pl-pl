---
title: 'Szybki start: tworzenie aplikacji ASP.NET Core w języku C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w usłudze Azure App Service, wdrażając domyślny szablon aplikacji sieci Web ASP.NET Core w programie Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 6c7ab1290033792cf356c4882811670011df5efe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086049"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Szybki start: tworzenie aplikacji sieci Web ASP.NET Core na platformie Azure

W tym przewodniku Szybki start dowiesz się, jak utworzyć i wdrożyć pierwszą ASP.NET podstawową aplikację sieci web w [usłudze Azure App Service.](overview.md) 

Po zakończeniu będziesz mieć grupę zasobów platformy Azure składającą się z planu hostingowego usługi App Service i usługi App Service z wdrożoną aplikacją sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/dotnet/).
- Ten szybki start wdraża aplikację w usłudze App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej platformy .NET Core w usłudze App Service w systemie Linux](./containers/quickstart-dotnetcore.md).
- Zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> z **obciążeniem ASP.NET i tworzenia sieci Web.**

  Jeśli program Visual Studio 2019 został już zainstalowany:

  - Zainstaluj najnowsze aktualizacje w programie Visual Studio, wybierając **pozycję Pomoc** > **Sprawdź dostępność aktualizacji**.
  - Dodaj obciążenie, wybierając **pozycję Narzędzia** > **Pobierz narzędzia i funkcje**.


## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

Utwórz ASP.NET podstawową aplikację internetową w programie Visual Studio, wykonując następujące kroki:

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**.

1. W **obszarze Tworzenie nowego projektu**wybierz ASP.NET **podstawową aplikację sieci Web** i potwierdź, że **c#** jest wyświetlany w językach dla tego wyboru, a następnie wybierz pozycję **Dalej**.

1. W **obszarze Konfiguruj nowy projekt**nazwij projekt aplikacji sieci web *myFirstAzureWebApp*i wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Można wdrożyć dowolny typ ASP.NET podstawowej aplikacji sieci web na platformie Azure, ale dla tego przewodnika Szybki start wybierz szablon **aplikacji sieci Web.** Upewnij **się,** że uwierzytelnianie jest ustawione na **Brak uwierzytelniania**i że nie jest zaznaczona żadna inna opcja. Następnie wybierz przycisk **Create** (Utwórz).

   ![Tworzenie nowej aplikacji sieci Web ASP.NET Core](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. Z menu Visual Studio wybierz **polecenie Debugowanie** > **start bez debugowania,** aby uruchomić aplikację sieci web lokalnie.

   ![Aplikacja sieci Web działająca lokalnie](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web

Aby opublikować aplikację sieci Web, należy najpierw utworzyć i skonfigurować nową usługę App Service, w której można opublikować aplikację. 

W ramach konfigurowania usługi App Service utworzysz:

- Nowa [grupa zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) zawierająca wszystkie zasoby platformy Azure dla usługi.
- Nowy [plan hostingowy](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) określający lokalizację, rozmiar i funkcje farmy serwerów www obsługujących aplikację.

Wykonaj następujące kroki, aby utworzyć usługę App Service i opublikować aplikację internetową:

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**. Jeśli nie zalogowano się jeszcze do konta platformy Azure w programie Visual Studio, wybierz pozycję **Dodaj konto** lub **Zaloguj się.** Można również utworzyć bezpłatne konto platformy Azure.

1. W oknie **dialogowym Wybieranie celu publikowania** wybierz pozycję **App Service**, wybierz pozycję **Utwórz nowy**, a następnie wybierz pozycję **Utwórz profil**.

   ![Wybieranie miejsca docelowego publikacji](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. W **usłudze aplikacji: Utwórz nowe** okno dialogowe podaj globalnie unikatową **nazwę** aplikacji, akceptując nazwę domyślną lub wprowadzając nową nazwę. Prawidłowe znaki `a-z` `A-Z`to: , , `0-9`i `-`. Ta **nazwa** jest używana jako prefiks adresu `http://<app_name>.azurewebsites.net`URL aplikacji internetowej w formacie .

1. W przypadku **subskrypcji**zaakceptuj subskrypcję, która jest wyświetlana na liście, lub wybierz nową z listy rozwijanej.

1. W **grupie Zasobów**wybierz pozycję **Nowy**. W **obszarze Nowa nazwa grupy zasobów**wprowadź *myResourceGroup* i wybierz przycisk **OK**. 

1. W przypadku **planu hostingowego**wybierz **nowy**. 

1. W **planie hostingu: Utwórz nowe** okno dialogowe wprowadź wartości określone w poniższej tabeli:

   | Ustawienie  | Sugerowana wartość | Opis |
   | -------- | --------------- | ----------- |
   | **Plan hostingowy**  | *myFirstAzureWebAppPlan* | Nazwa planu usługi App Service. |
   | **Lokalizacja**      | *Europa Zachodnia* | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | **Rozmiar**          | *Bezpłatna* | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |
   
   ![Tworzenie nowego planu hostingowego](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Pozostaw **aplikację Insights** ustawioną na *Brak*.

1. W **usłudze aplikacji: Tworzenie nowego** okna dialogowego wybierz pozycję **Utwórz,** aby rozpocząć tworzenie zasobów platformy Azure.

   ![Tworzenie nowej usługi aplikacji](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Po zakończeniu pracy kreatora wybierz pozycję **Publikuj**.

   ![Publikowanie aplikacji sieci Web na platformie Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Program Visual Studio publikuje aplikację sieci Web ASP.NET Core na platformie Azure i uruchamia aplikację w domyślnej przeglądarce. 

   ![Opublikowana ASP.NET aplikacji sieci Web działającej na platformie Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Gratulacje!** Twoja aplikacja sieci Web ASP.NET Core działa na żywo w usłudze Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

Wykonaj następujące kroki, aby zaktualizować i ponownie wdrożyć aplikację sieci web:

1. W **Eksploratorze rozwiązań**w ramach projektu otwórz **stronę Pages** > **Index.cshtml**.

1. Zastąp cały `<div>` tag następującym kodem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**.

1. Na stronie **Podsumowanie publikowania** wybierz pozycję **Publikuj**.

   ![Publikowanie aktualizacji w aplikacji sieci Web](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

![Zaktualizowana ASP.NET aplikacji sieci Web działającej na platformie Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby zarządzać aplikacją internetową, przejdź do [witryny Azure portal](https://portal.azure.com)oraz wyszukaj i wybierz pozycję **Usługi aplikacji**.

![Wybieranie usług aplikacji](./media/app-service-web-get-started-dotnet/app-services.png)

Na stronie **Usługi aplikacji** wybierz nazwę aplikacji sieci Web.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-dotnet/select-app-service.png)

Strona **Przegląd** aplikacji sieci Web zawiera opcje zarządzania podstawowego, takie jak przeglądanie, zatrzymywania, uruchamianie, ponowne uruchamianie i usuwanie. Lewe menu zawiera kolejne strony do konfigurowania aplikacji.

![Usługa aplikacji w witrynie Azure portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto programu Visual Studio do utworzenia i wdrożenia aplikacji sieci web ASP.NET Core w usłudze Azure App Service.

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć aplikację .NET Core i połączyć ją z bazą danych SQL:

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database (Platforma ASP.NET Core z usługą SQL Database)](app-service-web-tutorial-dotnetcore-sqldb.md)
