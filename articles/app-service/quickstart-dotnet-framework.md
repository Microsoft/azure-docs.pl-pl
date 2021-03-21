---
title: 'Szybki Start: Tworzenie aplikacji ASP.NET w języku C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w Azure App Service, wdrażając domyślny szablon aplikacji sieci Web w języku C# ASP.NET z poziomu programu Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: a4f7ba288bc27d6079deea9caf0ea315a55d0745
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004095"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Tworzenie aplikacji internetowej środowiska ASP.NET Framework na platformie Azure

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.

Ten przewodnik Szybki start zawiera informacje dotyczące sposobu wdrożenia pierwszej aplikacji internetowej ASP.NET w usłudze Azure App Service. Po zakończeniu będziesz mieć plan App Service. Będziesz również mieć aplikację App Service ze wdrożoną aplikacją sieci Web.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, zainstaluj <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> z obciążeniem **programowania ASP.NET i sieci Web** .

Jeśli zainstalowano już program Visual Studio 2019:

- Zainstaluj najnowsze aktualizacje programu Visual Studio, wybierając pozycję **Pomoc**  >  **Sprawdź dostępność aktualizacji**.
- Dodaj obciążenie, wybierając kolejno pozycje **Narzędzia**  >  **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-web-app"></a>Tworzenie aplikacji sieci Web ASP.NET <a name="create-and-publish-the-web-app"></a>

Utwórz aplikację internetową ASP.NET, wykonując następujące czynności:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

2. W obszarze **Utwórz nowy projekt** Znajdź i wybierz pozycję **aplikacja sieci Web ASP.NET (.NET Framework)**, a następnie wybierz pozycję **dalej**.

3. W obszarze **Konfigurowanie nowego projektu** Nadaj nazwę aplikacji _myFirstAzureWebApp_, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej platformy ASP.NET. Na potrzeby tego przewodnika Szybki Start wybierz szablon **MVC** .

5. Upewnij się, że uwierzytelnianie jest ustawione na wartość **bez uwierzytelniania**. Wybierz przycisk **Utwórz**.

   ![Tworzenie aplikacji sieci Web ASP.NET](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. Z menu programu Visual Studio wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację internetową lokalnie.

   ![Uruchamianie aplikacji lokalnie](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web <a name="launch-the-publish-wizard"></a>

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**.

1. W obszarze **Publikowanie** wybierz pozycję **Azure** , a następnie kliknij przycisk **dalej**.

1. Wybierz pozycję **Azure App Service (Windows)** , a następnie kliknij przycisk **dalej**.

   <!-- ![Publish from project overview page](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png) -->

1. Twoje opcje zależą od tego, czy użytkownik jest już zalogowany na platformie Azure i czy masz konto programu Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto** lub **Zaloguj** się, aby zalogować się do subskrypcji platformy Azure. Jeśli użytkownik jest już zalogowany, wybierz odpowiednie konto.

   ![Logowanie do platformy Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Po prawej stronie **wystąpień App Service** kliknij pozycję **+** .

   ![Nowa aplikacja App Service](./media/quickstart-dotnet-framework/publish-new-app-service.png)

1. W obszarze **Grupa zasobów** wybierz pozycję **Nowy**.

1. W polu **Nazwa nowej grupy zasobów wprowadź nazwę** *zasobu* , a następnie wybierz **przycisk OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. W obszarze **Plan hostingu** wybierz pozycję **Nowy**.

1. W oknie dialogowym **Plan hostingu** wprowadź wartości z poniższej tabeli, a następnie wybierz przycisk **OK**.

   | Ustawienie | Sugerowana wartość | Opis |
   |-|-|-|
   | Plan hostingu| myAppServicePlan | Nazwa planu usługi App Service. |
   | Lokalizacja | West Europe | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

   ![Tworzenie planu usługi App Service](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. W polu **Nazwa** wprowadź unikatową nazwę aplikacji, która zawiera tylko prawidłowe znaki to `a-z` , `A-Z` , `0-9` i `-` . Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app-name>.azurewebsites.net`, gdzie `<app-name>` to nazwa aplikacji.

2. Wybierz pozycję **Utwórz** , aby utworzyć zasoby platformy Azure.

   ![Konfigurowanie nazwy aplikacji](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    Po zakończeniu działania kreatora zasoby platformy Azure są tworzone dla Ciebie i wszystko jest gotowe do opublikowania.

3. Wybierz pozycję **Zakończ** , aby zamknąć kreatora.

3. Na stronie **Publikowanie** kliknij pozycję **Publikuj**. Program Visual Studio kompiluje, pakuje i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

    ![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/quickstart-dotnet-framework/published-azure-web-app.png)

Nazwa aplikacji określona na **App Service Utwórz nową** stronę jest używana jako prefiks adresu URL w formacie `http://<app-name>.azurewebsites.net` .

**Gratulacje!** Aplikacja sieci Web ASP.NET działa na żywo w Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

1. W **Eksplorator rozwiązań** w ramach projektu Otwórz **Widok**  >  **główny**  >  **index. cshtml**.

1. Znajdź tag HTML `<div class="jumbotron">` u góry i zastąp cały element następującym kodem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**. Następnie wybierz pozycję **Publikuj**.

    Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

    ![Zaktualizowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

1. Aby zarządzać aplikacją sieci Web, przejdź do [Azure Portal](https://portal.azure.com), a następnie wyszukaj i wybierz pozycję **App Services**.

   ![Wybieranie usługi App Services](./media/quickstart-dotnet-framework/app-services.png)

2. Na stronie **App Services** wybierz nazwę aplikacji sieci Web.

   ![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   Zostanie wyświetlona strona Omówienie aplikacji internetowej. W tym miejscu można wykonać podstawowe zarządzanie, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

   ![Omówienie App Service w Azure Portal](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Platforma .ASP.NET z usługą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET](configure-language-dotnet-framework.md)
