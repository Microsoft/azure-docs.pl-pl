---
title: Konfigurowanie ciągłego wdrażania
description: Dowiedz się, jak włączyć CI/CD do Azure App Service z usługi GitHub, BitBucket, Azure Repos lub innych repozytoriów. Wybierz potok kompilacji, który odpowiada Twoim potrzebom.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: b7730558e2a660b0cf00a5b6962d1e2275dd472c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984393"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie do Azure App Service

[Azure App Service](overview.md) umożliwia ciągłe wdrażanie z repozytorium GitHub, BitBucket i [Azure Repos](https://azure.microsoft.com/services/devops/repos/) repozytoriów przez ściąganie najnowszych aktualizacji. W tym artykule pokazano, jak używać Azure Portal do ciągłego wdrażania aplikacji za pomocą usługi kompilacji kudu lub [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Aby uzyskać więcej informacji na temat usług kontroli źródła, zobacz [Tworzenie repozytorium (GitHub)], [Tworzenie repozytorium (BitBucket)]lub [tworzenie nowego repozytorium git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autoryzuj Azure App Service 

Aby użyć Azure Repos, upewnij się, że organizacja usługi Azure DevOps jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci Web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).

W przypadku usługi BitBucket lub GitHub Autoryzuj Azure App Service, aby nawiązać połączenie z repozytorium. Wystarczy tylko autoryzować z usługą kontroli źródła. 

1. W [Azure Portal](https://portal.azure.com)Wyszukaj  **App Services** i wybierz pozycję.

   ![Wyszukaj usługi App Services.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Wybierz App Service, które chcesz wdrożyć.

   ![Wybierz aplikację.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.
   
1. Na stronie **centrum wdrażania** wybierz pozycję **GitHub** lub **BitBucket**, a następnie wybierz pozycję **Autoryzuj**. 
   
   ![Wybierz pozycję Usługa kontroli źródła, a następnie wybierz pozycję Autoryzuj.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. W razie potrzeby zaloguj się do usługi i postępuj zgodnie z monitami o autoryzację. 

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie 

Po zatwierdzeniu usługi kontroli źródła Skonfiguruj aplikację do ciągłego wdrażania za pomocą wbudowanego serwera [Kudu App Service](#option-1-kudu-app-service) build lub [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Opcja 1: kudu App Service

Wbudowanego kudu App Service Build Server można użyć do ciągłego wdrażania z usługi GitHub, BitBucket lub Azure Repos. 

1. W [Azure Portal](https://portal.azure.com)Wyszukaj **App Services**, a następnie wybierz App Service do wdrożenia. 
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.
   
1. Wybierz autoryzowanego dostawcę kontroli źródła na stronie **centrum wdrażania** , a następnie wybierz pozycję **Kontynuuj**. W witrynie GitHub lub BitBucket można także wybrać pozycję **Zmień konto** , aby zmienić autoryzowane konto. 
   
   > [!NOTE]
   > Aby użyć Azure Repos, upewnij się, że Azure DevOps Services organizacja jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci Web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
   
1. W witrynie GitHub lub Azure Repos na stronie **dostawca kompilacji** wybierz pozycję **App Service usługa kompilacji**, a następnie wybierz pozycję **Kontynuuj**. BitBucket zawsze używa usługi kompilacji App Service.
   
   ![Wybierz pozycję App Service Build Service, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na stronie **Konfigurowanie** :
   
   - W witrynie GitHub wybierz pozycję **organizacja**, **repozytorium**i **gałąź** , którą chcesz stale wdrożyć.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Azure App Service w serwisie GitHub. Przejdź do repozytorium GitHub i przejdź do pozycji **Ustawienia**  >  **aplikacje**  >  **autoryzowane aplikacje uwierzytelniania OAuth**. Wybierz pozycję **Azure App Service**, a następnie wybierz pozycję **Udziel**. W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby przyznać uprawnienia.
     
   - W przypadku BitBucket wybierz **zespół**BitBucket, **repozytorium**i **gałąź** , które chcesz wdrożyć w sposób ciągły.
     
   - W obszarze Azure Repos wybierz organizację, **projekt**, **repozytorium**i **gałąź** **usługi Azure DevOps**, która ma być ciągle wdrażana.
     
     > [!NOTE]
     > Jeśli Twoja organizacja usługi Azure DevOps nie jest wymieniona na liście, upewnij się, że została połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci Web](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
     
1. Wybierz pozycję **Continue** (Kontynuuj).
   
   ![Wypełnij pola informacje o repozytorium, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie** , a następnie wybierz pozycję **Zakończ**.
   
1. Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w aplikacji App Service. Zatwierdzenia i wdrożenia można śledzić na stronie **centrum wdrażania** .
   
   ![Śledzenie zatwierdzeń i wdrożeń w centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opcja 2: Azure Pipelines 

Jeśli Twoje konto ma wymagane uprawnienia, możesz skonfigurować Azure Pipelines do ciągłego wdrażania z usługi GitHub lub Azure Repos. Aby uzyskać więcej informacji o wdrażaniu za pomocą Azure Pipelines, zobacz [wdrażanie aplikacji sieci Web na platformie Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać Azure App Service tworzenia ciągłego dostarczania przy użyciu Azure Pipelines, organizacja usługi Azure DevOps powinna mieć następujące uprawnienia: 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisu w Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć rolę **właściciela** w ramach subskrypcji platformy Azure.

- Musisz być administratorem w projekcie usługi Azure DevOps, którego chcesz użyć.

#### <a name="github--azure-pipelines"></a>GitHub i Azure Pipelines

1. W [Azure Portal](https://portal.azure.com)Wyszukaj **App Services**, a następnie wybierz App Service do wdrożenia. 
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.

1. Wybierz pozycję **GitHub** jako dostawcę kontroli źródła na stronie **centrum wdrażania** , a następnie wybierz pozycję **Kontynuuj**. W przypadku usługi **GitHub**możesz wybrać pozycję **Zmień konto** , aby zmienić autoryzowane konto.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::
   
1. Na stronie **dostawca kompilacji** wybierz pozycję **Azure Pipelines (wersja zapoznawcza)**, a następnie wybierz pozycję **Kontynuuj**.

    :::image type="content" source="media/app-service-continuous-deployment/select-build-provider.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::
   
1. Na stronie **Konfigurowanie** w sekcji **kod** wybierz **organizację**, **repozytorium**i **gałąź** , którą chcesz wdrożyć w sposób ciągły, a następnie wybierz pozycję **Kontynuuj**.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Azure App Service w serwisie GitHub. Przejdź do repozytorium GitHub i przejdź do pozycji **Ustawienia**  >  **aplikacje**  >  **autoryzowane aplikacje uwierzytelniania OAuth**. Wybierz pozycję **Azure App Service**, a następnie wybierz pozycję **Udziel**. W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby przyznać uprawnienia.
       
    W sekcji **kompilacja** Określ organizację usługi Azure DevOps, projekt, strukturę języka, która Azure Pipelines powinna być używana do uruchamiania zadań kompilacji, a następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::

1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie** , a następnie wybierz pozycję **Zakończ**.

   :::image type="content" source="media/app-service-continuous-deployment/summary.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::
   
1. Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w App Service. Zatwierdzenia i wdrożenia można śledzić na stronie **centrum wdrażania** .
   
   ![Śledzenie zatwierdzeń i wdrożeń w centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. W [Azure Portal](https://portal.azure.com)Wyszukaj **App Services**, a następnie wybierz App Service do wdrożenia. 
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.

1. Wybierz **Azure Repos** jako dostawcę kontroli źródła na stronie **centrum wdrażania** , a następnie wybierz pozycję **Kontynuuj**.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::

1. Na stronie **dostawca kompilacji** wybierz pozycję **Azure Pipelines (wersja zapoznawcza)**, a następnie wybierz pozycję **Kontynuuj**.

    :::image type="content" source="media/app-service-continuous-deployment/azure-pipelines.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::

1. Na stronie **Konfigurowanie** w sekcji **kod** wybierz **organizację**, **repozytorium**i **gałąź** , którą chcesz wdrożyć w sposób ciągły, a następnie wybierz pozycję **Kontynuuj**.

   > [!NOTE]
   > Jeśli istniejąca organizacja usługi Azure DevOps nie jest wymieniona, może być konieczne połączenie jej z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Definiowanie potoku wydania dysku CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   W sekcji **kompilacja** Określ organizację usługi Azure DevOps, projekt, strukturę języka, która Azure Pipelines powinna być używana do uruchamiania zadań kompilacji, a następnie wybierz pozycję **Kontynuuj**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::

1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie** , a następnie wybierz pozycję **Zakończ**.  
     
   :::image type="content" source="media/app-service-continuous-deployment/summary-azure-pipelines.png" alt-text="Zrzut ekranu przedstawiający stronę centrum wdrażania App Service.":::

1. Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w App Service. Zatwierdzenia i wdrożenia można śledzić na stronie **centrum wdrażania** .

## <a name="disable-continuous-deployment"></a>Wyłączanie ciągłego wdrażania

Aby wyłączyć ciągłe wdrażanie, wybierz pozycję **Rozłącz** w górnej części strony **centrum wdrażania** aplikacji.

![Wyłączanie ciągłego wdrażania](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Użyj nieobsługiwanych repozytoriów

W przypadku aplikacji systemu Windows możesz ręcznie skonfigurować ciągłe wdrażanie z repozytorium Git lub Mercurial w chmurze, które nie jest bezpośrednio obsługiwane przez portal, na przykład [GitLab](https://gitlab.com/). Możesz to zrobić, wybierając pole zewnętrzne na stronie **centrum wdrażania** . Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego wdrażania przy użyciu kroków ręcznych](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zbadaj typowe problemy związane z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Korzystanie z programu Azure PowerShell](/powershell/azure/)
* [Dokumentacja usługi git](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Tworzenie repozytorium (GitHub)]: https://help.github.com/articles/create-a-repo
[Tworzenie repozytorium (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Utwórz nowe repozytorium git (Azure Repos)]: /azure/devops/repos/git/creatingrepo