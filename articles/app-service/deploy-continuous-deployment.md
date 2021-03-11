---
title: Konfigurowanie ciągłego wdrażania
description: Dowiedz się, jak włączyć CI/CD do Azure App Service z usługi GitHub, BitBucket, Azure Repos lub innych repozytoriów. Wybierz potok kompilacji, który odpowiada Twoim potrzebom.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/03/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 5af8294518759181326e7736ef755f0a83581014
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564967"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie do usługi Azure App Service

[Azure App Service](overview.md) umożliwia ciągłe wdrażanie z repozytorium [GitHub](https://help.github.com/articles/create-a-repo), [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)i [Azure Repos](/azure/devops/repos/git/creatingrepo) repozytoriów przez ściąganie najnowszych aktualizacji.

> [!NOTE]
> Strona **centrum deweloperskiego (klasyczna)** w Azure Portal, która jest starym środowiskiem wdrażania, będzie przestarzała w marcu 2021. Ta zmiana nie wpłynie na żadne istniejące ustawienia wdrożenia w aplikacji. można nadal zarządzać wdrażaniem aplikacji na stronie **centrum wdrażania** .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Skonfiguruj źródło wdrożenia

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji App Service.

1. W menu po lewej stronie kliknij pozycję Ustawienia **centrum wdrażania**  >  . 

1. W obszarze **Źródło** wybierz jedną z opcji ciągłej integracji/ciągłego wdrażania.

    ![Pokazuje, jak wybrać źródło wdrożenia w programie Deployment Center dla Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Wybierz kartę, która odnosi się do wybranej czynności.

# <a name="github"></a>[GitHub](#tab/github)

4. [Akcje GitHub](#how-the-github-actions-build-provider-works) są domyślnymi dostawcami kompilacji. Aby to zmienić, kliknij przycisk **Zmień dostawcę**  >  **App Service kompilacji usługi** (kudu) > **OK**.

    > [!NOTE]
    > Aby użyć Azure Pipelines jako dostawcy kompilacji dla aplikacji App Service, nie Konfiguruj go w App Service. Zamiast tego Skonfiguruj ciągłą konfigurację/ciągłego dostarczania za pomocą Azure Pipelines. Opcja **Azure Pipelines** tylko wskazuje, że jesteś w odpowiednim kierunku.

1. Jeśli po raz pierwszy wdrażasz dane z usługi GitHub, kliknij przycisk **Autoryzuj** i postępuj zgodnie z monitami o autoryzację. Jeśli chcesz wdrożyć z repozytorium innego użytkownika, kliknij przycisk **Zmień konto**.

1. Po autoryzowaniu konta platformy Azure za pomocą usługi GitHub wybierz **organizację**, **repozytorium** i **gałąź** , aby skonfigurować usługę ciągłej integracji/ciągłego dostarczania.

1. Gdy akcje usługi GitHub są wybranym dostawcą kompilacji, możesz wybrać odpowiedni plik przepływu pracy z listami rozwijanymi stos i **wersja** **środowiska uruchomieniowego** . Platforma Azure zatwierdzi ten plik przepływu pracy w wybranym repozytorium GitHub w celu obsługi zadań kompilowania i wdrażania. Aby wyświetlić plik przed zapisaniem zmian, kliknij przycisk **Podgląd pliku**.

    > [!NOTE]
    > App Service wykrywa [Ustawienia stosu języka](configure-common.md#configure-language-stack-settings) aplikacji i wybiera najbardziej odpowiedni szablon przepływu pracy. Jeśli wybierzesz inny szablon, może on wdrożyć aplikację, która nie działa prawidłowo. Aby uzyskać więcej informacji, zobacz [jak działa dostawca kompilacji akcji usługi GitHub](#how-the-github-actions-build-provider-works).

1. Kliknij pozycję **Zapisz**.
   
    Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w aplikacji App Service. Zatwierdzenia i wdrożenia można śledzić na karcie **dzienniki** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

Integracja BitBucket używa usług kompilacji App Service (kudu) do automatyzacji kompilacji.

4. Jeśli po raz pierwszy wdrażasz program z BitBucket, kliknij przycisk **Autoryzuj** i postępuj zgodnie z monitami o autoryzację. Jeśli chcesz wdrożyć z repozytorium innego użytkownika, kliknij przycisk **Zmień konto**.

1. W przypadku BitBucket wybierz **zespół** BitBucket, **repozytorium** i **gałąź** , które chcesz wdrożyć w sposób ciągły.

1. Kliknij pozycję **Zapisz**.
   
    Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w aplikacji App Service. Zatwierdzenia i wdrożenia można śledzić na karcie **dzienniki** .
   
# <a name="local-git"></a>[Lokalny system Git](#tab/local)

Zobacz [lokalne wdrożenie narzędzia Git, aby Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos jako źródło wdrożenia obsługuje aplikacje systemu Windows.
>

4. App Service Build Service (kudu) jest domyślnym dostawcą kompilacji.

    > [!NOTE]
    > Aby użyć Azure Pipelines jako dostawcy kompilacji dla aplikacji App Service, nie Konfiguruj go w App Service. Zamiast tego Skonfiguruj ciągłą konfigurację/ciągłego dostarczania za pomocą Azure Pipelines. Opcja **Azure Pipelines** tylko wskazuje, że jesteś w odpowiednim kierunku.

1. Wybierz organizację, **projekt**, **repozytorium** i **gałąź** **usługi Azure DevOps**, która ma być ciągle wdrażana. 

    Jeśli Twoja organizacja DevOps nie znajduje się na liście, nie jest jeszcze połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia z usługą platformy Azure](/azure/devops/pipelines/library/connect-to-azure).

-----

## <a name="disable-continuous-deployment"></a>Wyłączanie ciągłego wdrażania

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji App Service.

1. W menu po lewej stronie kliknij pozycję Ustawienia **centrum wdrażania**  >    >  **Rozłącz**. 

    ![Pokazuje, jak rozłączyć synchronizację folderu w chmurze z aplikacją App Service w Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Domyślnie plik przepływu pracy akcji usługi GitHub jest zachowywany w repozytorium, ale nadal będzie wyzwalał wdrożenie w aplikacji. Aby usunąć go z repozytorium, wybierz pozycję **Usuń plik przepływu pracy**.

1. Kliknij przycisk **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Jak działa dostawca kompilacji akcji usługi GitHub

Dostawca kompilacji akcji usługi GitHub jest opcją dla ciągłej integracji [/](#configure-deployment-source)ciągłego wdrażania i wykonuje następujące czynności w celu skonfigurowania ciągłej integracji/ciągłego wdrażania:

- Umieszcza w repozytorium GitHub plik przepływu pracy akcji usługi GitHub w celu obsługi kompilowania i wdrażania zadań do App Service.
- Dodaje profil publikowania aplikacji jako wpis tajny usługi GitHub. Plik przepływu pracy używa tego klucza tajnego do uwierzytelniania za pomocą App Service.
- Przechwytuje informacje z [dzienników przebiegów przepływu pracy](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) i wyświetla je na karcie **dzienniki** w **centrum wdrażania** aplikacji.

Dostawcę kompilacji akcji usługi GitHub można dostosować w następujący sposób:

- Dostosuj plik przepływu pracy po jego wygenerowaniu w repozytorium GitHub. Aby uzyskać więcej informacji, zobacz [składnia przepływu pracy dla akcji usługi GitHub](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Upewnij się, że przepływ pracy jest wdrażany do App Service za pomocą akcji [Azure/webapps — Deploy](https://github.com/Azure/webapps-deploy) .
- Jeśli wybrana gałąź jest chroniona, nadal można wyświetlić podgląd pliku przepływu pracy bez zapisywania konfiguracji, a następnie dodać go ręcznie do repozytorium. Ta metoda nie zapewnia integracji dzienników z Azure Portal.
- Zamiast profilu publikowania należy wdrożyć przy użyciu [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) w Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Uwierzytelnianie za pomocą nazwy głównej usługi

1. Wygeneruj jednostkę usługi za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) w [interfejsie CLI platformy Azure](/cli/azure/). W poniższym przykładzie Zastąp *\<subscription-id>* wartości, *\<group-name>* i *\<app-name>* własnymi wartościami:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > W celu zapewnienia bezpieczeństwa należy przyznać minimalny wymagany dostęp do nazwy głównej usługi. Zakres w poprzednim przykładzie jest ograniczony do konkretnej aplikacji App Service, a nie całej grupy zasobów.
    
1. Zapisz wszystkie dane wyjściowe JSON dla następnego kroku, w tym najwyższego poziomu `{}` .

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

1. Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Nadaj wpisowi tajnemu nazwę, taką jak `AZURE_CREDENTIALS` .

1. W pliku przepływu pracy generowanym przez **centrum wdrażania** Popraw `azure/webapps-deploy` krok przy użyciu kodu, takiego jak Poniższy przykład (który jest modyfikowany z pliku przepływu pracy Node.js):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Wdrażanie z innych repozytoriów

W przypadku aplikacji systemu Windows możesz ręcznie skonfigurować ciągłe wdrażanie z repozytorium Git lub Mercurial w chmurze, które nie jest bezpośrednio obsługiwane przez portal, na przykład [GitLab](https://gitlab.com/). Możesz to zrobić, wybierając z listy rozwijanej **Źródło** zewnętrzny program git. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego wdrażania przy użyciu kroków ręcznych](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="more-resources"></a>Więcej zasobów

* [Wdrażanie z Azure Pipelines na platformie Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Zbadaj typowe problemy związane z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Korzystanie z programu Azure PowerShell](/powershell/azure/)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
