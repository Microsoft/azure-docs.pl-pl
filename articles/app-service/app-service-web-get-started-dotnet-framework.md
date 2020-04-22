---
title: 'Szybki start: tworzenie aplikacji ASP.NET języka C#'
description: Dowiedz się, jak uruchamiać aplikacje sieci Web w usłudze Azure App Service, wdrażając domyślny szablon aplikacji sieci Web ASP.NET języka C# z programu Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 04/21/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 47a5cf2586b8146a68772962c60dc0dbdbb7e2fe
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768884"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Tworzenie aplikacji internetowej środowiska ASP.NET Framework na platformie Azure

[Usługa Azure App Service](overview.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingu.

Ten przewodnik Szybki start zawiera informacje dotyczące sposobu wdrożenia pierwszej aplikacji internetowej ASP.NET w usłudze Azure App Service. Po zakończeniu będziesz mieć plan usługi app service. Będziesz mieć również aplikację usługi App Service z wdrożoną aplikacją sieci web.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, należy zainstalować <a href="https://www.visualstudio.com/downloads/" target="_blank">program Visual Studio 2019</a> z **obciążeniem ASP.NET i tworzenia sieci Web.**

Jeśli program Visual Studio 2019 został już zainstalowany:

- Zainstaluj najnowsze aktualizacje w programie Visual Studio, wybierając **pozycję Pomoc** > **Sprawdź dostępność aktualizacji**.
- Dodaj obciążenie, wybierając **pozycję Narzędzia** > **Pobierz narzędzia i funkcje**.

## <a name="create-an-aspnet-web-app"></a>Tworzenie ASP.NET aplikacji sieci Web<a name="create-and-publish-the-web-app"></a>

Utwórz ASP.NET aplikacji sieci web, wykonując następujące kroki:

1. Otwórz program Visual Studio, a następnie wybierz pozycję **Utwórz nowy projekt**.

2. W **obszarze Tworzenie nowego projektu**znajdź i wybierz pozycję ASP.NET aplikacji sieci Web **(.NET Framework)** wybierz pozycję **Dalej**.

3. W **obszarze Konfiguruj nowy projekt nazwij**aplikację _myFirstAzureWebApp_, a następnie wybierz pozycję **Utwórz**.

   ![Konfigurowanie projektu aplikacji sieci Web](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Na platformie Azure można wdrożyć dowolny typ aplikacji internetowej platformy ASP.NET. W przypadku tego przewodnika Szybki start wybierz szablon **MVC.**

5. Upewnij się, że uwierzytelnianie jest ustawione na **Brak uwierzytelniania**. Wybierz pozycję **Utwórz**.

   ![Tworzenie ASP.NET aplikacji sieci Web](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Z menu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić aplikację sieci web lokalnie.

   ![Uruchamianie aplikacji lokalnie](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publikowanie aplikacji sieci Web<a name="launch-the-publish-wizard"></a>

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** i wybierz polecenie **Publikuj**.

1. Wybierz **pozycję Usługa aplikacji** i wybierz pozycję **Utwórz profil**.

   ![Publikowanie ze strony przeglądu projektu](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. W **usłudze App Service Tworzenie nowych**opcji zależy od tego, czy jesteś już zalogowany na platformie Azure i czy masz konto programu Visual Studio połączone z kontem platformy Azure. Wybierz pozycję **Dodaj konto** lub Zaloguj **się,** aby zalogować się do subskrypcji platformy Azure. Jeśli jesteś już zalogowany, wybierz konto, które chcesz.

   > [!NOTE]
   > Jeśli przeprowadzono już logowanie, nie wybieraj jeszcze pozycji **Utwórz**.
   >
   >

   ![Logowanie do platformy Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. W obszarze **Grupa zasobów**wybierz pozycję **Nowy**.

1. W **obszarze Nowa nazwa grupy zasobów**wprowadź *myResourceGroup* i wybierz przycisk **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Aby uzyskać **plan hostingowy,** wybierz **nowy**.

1. W oknie dialogowym **Konfigurowanie planu hostingu** wprowadź wartości z poniższej tabeli, a następnie wybierz przycisk **OK**.

   | Ustawienie | Sugerowana wartość | Opis |
   |-|-|-|
   | Plan hostingu| myAppServicePlan | Nazwa planu usługi App Service. |
   | Lokalizacja | Europa Zachodnia | Centrum danych, w którym hostowana jest aplikacja internetowa. |
   | Rozmiar | Bezpłatna | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) określa funkcje hostowania. |

   ![Tworzenie planu usługi App Service](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. W **programie Name**wprowadź unikatową nazwę aplikacji `a-z`zawierającą tylko prawidłowe znaki , `A-Z`i `0-9`. `-` Możesz zaakceptować automatycznie wygenerowaną unikatową nazwę. Adres URL aplikacji internetowej to `http://<app-name>.azurewebsites.net`, gdzie `<app-name>` to nazwa aplikacji.

2. Wybierz pozycję **Utwórz**, aby rozpocząć tworzenie zasobów platformy Azure.

   ![Konfigurowanie nazwy aplikacji](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

    Po zakończeniu pracy kreatora zasoby platformy Azure są tworzone dla Ciebie i można go opublikować.

3. Na stronie **Publikowanie** kliknij pozycję **Publikuj**. Visual Studio tworzy, pakiety i publikuje aplikację na platformie Azure, a następnie uruchamia aplikację w przeglądarce domyślnej.

    ![Opublikowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Nazwa aplikacji określona na stronie **Tworzenie nowej usługi aplikacji** jest `http://<app-name>.azurewebsites.net`używana jako prefiks adresu URL w formacie .

**Gratulacje!** Twoja ASP.NET aplikacja internetowa działa na żywo w usłudze Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizowanie aplikacji i ponowne wdrażanie

1. W **Eksploratorze rozwiązań**w ramach projektu otwórz **program Views** > **Home** > **Index.cshtml**.

1. Znajdź tag HTML `<div class="jumbotron">` u góry i zastąp cały element następującym kodem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Aby przeprowadzić ponowne wdrożenie na platformie Azure, kliknij prawym przyciskiem myszy projekt **myFirstAzureWebApp** w **Eksploratorze rozwiązań**, a następnie wybierz polecenie **Publikuj**. Następnie wybierz pozycję **Publikuj**.

    Po zakończeniu publikowania program Visual Studio otworzy w przeglądarce adres URL aplikacji internetowej.

    ![Zaktualizowana aplikacja internetowa platformy ASP.NET na platformie Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

1. Aby zarządzać aplikacją internetową, przejdź do [witryny Azure portal](https://portal.azure.com)oraz wyszukaj i wybierz pozycję **Usługi aplikacji**.

   ![Wybierz usługi aplikacji](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Na stronie **Usługi aplikacji** wybierz nazwę aplikacji sieci Web.

   ![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Zostanie wyświetlona strona Omówienie aplikacji internetowej. W tym miejscu możesz wykonywać podstawowe zarządzanie, takie jak przeglądanie, zatrzymywania, uruchamianie, ponowne uruchamianie i usuwanie.

   ![Omówienie usługi app service w witrynie Azure portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Platforma .ASP.NET z usługą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
