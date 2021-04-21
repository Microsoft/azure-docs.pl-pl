---
title: Konfigurowanie ciągłego wdrażania
description: Dowiedz się, jak włączyć funkcję Azure App Service/CD z repozytoriów GitHub, BitBucket, Azure Repos lub innych. Wybierz potok kompilacji, który odpowiada Twoim potrzebom.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 633d62fc69c516b482d5749a07052337dc71f567
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789487"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie do usługi Azure App Service

[Azure App Service](overview.md) ciągłe wdrażanie z repozytoriów [GitHub,](https://help.github.com/articles/create-a-repo) [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html) [i Azure Repos](/azure/devops/repos/git/creatingrepo) przez ściąganie najnowszych aktualizacji.

> [!NOTE]
> Strona **Centrum opracowywania (klasyczna)** w Azure Portal, która jest starym środowiskom wdrażania, zostanie wycofana w marcu 2021 r. Ta zmiana nie wpłynie na żadne ustawienia wdrożenia istniejące w aplikacji i będzie można nadal zarządzać wdrażaniem aplikacji na **stronie Centrum wdrażania.**

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Konfigurowanie źródła wdrożenia

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania dla swojej App Service aplikacji.

1. W menu po lewej stronie kliknij pozycję **Ustawienia Centrum**  >  **wdrażania.** 

1. W **źródle** wybierz jedną z opcji ci/CD.

    ![Pokazuje, jak wybrać źródło wdrożenia w Centrum wdrażania dla Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Wybierz kartę odpowiadającą wybranemu krokowi.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) jest domyślnym dostawcą kompilacji. Aby ją zmienić, kliknij pozycję **Zmień dostawcę**  >  **App Service Build Service** (Kudu) > **OK.**

    > [!NOTE]
    > Aby używać Azure Pipelines jako dostawcy kompilacji dla aplikacji App Service, nie należy konfigurować jej w App Service. Zamiast tego należy skonfigurować ciasną/cd bezpośrednio z Azure Pipelines. Opcja **Azure Pipelines** wskazuje tylko właściwy kierunek.

1. Jeśli wdrażasz z usługi GitHub po raz pierwszy, kliknij pozycję **Autoryzuj** i postępuj zgodnie z monitami autoryzacji. Jeśli chcesz wdrożyć z repozytorium innego użytkownika, kliknij pozycję **Zmień konto.**

1. Po autoryzacji konta platformy Azure w usłudze GitHub  wybierz pozycję **Organizacja,** **Repozytorium** i Gałąź, aby skonfigurować ci/CD.

1. Jeśli GitHub Actions jest wybranym dostawcą kompilacji, możesz wybrać plik  przepływu pracy za pomocą listy rozwijanej Stos środowiska uruchomieniowego **i** Wersja. Platforma Azure zatwierdza ten plik przepływu pracy w wybranym repozytorium GitHub w celu obsługi zadań kompilacji i wdrażania. Aby wyświetlić plik przed zapisaniem zmian, kliknij pozycję **Podgląd pliku**.

    > [!NOTE]
    > App Service wykrywa ustawienie [stosu języka](configure-common.md#configure-language-stack-settings) aplikacji i wybiera najbardziej odpowiedni szablon przepływu pracy. Jeśli wybierzesz inny szablon, może on wdrożyć aplikację, która nie działa prawidłowo. Aby uzyskać więcej informacji, zobacz How the GitHub Actions build provider works (Jak [działa GitHub Actions kompilacji).](#how-the-github-actions-build-provider-works)

1. Kliknij pozycję **Zapisz**.
   
    Nowe zatwierdzenia w wybranym repozytorium i gałęzi są teraz wdrażane w sposób ciągły w App Service aplikacji. Zatwierdzenia i wdrożenia można śledzić na karcie **Dzienniki.**

# <a name="bitbucket"></a>[Bitbucket](#tab/bitbucket)

Integracja usługi BitBucket używa usługi App Service Build Services (Kudu) do automatyzacji kompilacji.

4. Jeśli wdrażasz z usługi BitBucket po raz pierwszy, kliknij przycisk **Autoryzuj** i postępuj zgodnie z monitami autoryzacji. Jeśli chcesz wdrożyć z repozytorium innego użytkownika, kliknij pozycję **Zmień konto.**

1. W przypadku aplikacji Bitbucket wybierz zespół Bitbucket, repozytorium **i** gałąź, **którą** chcesz stale wdrażać.

1. Kliknij pozycję **Zapisz**.
   
    Nowe zatwierdzenia w wybranym repozytorium i gałęzi są teraz wdrażane w sposób ciągły w App Service aplikacji. Zatwierdzenia i wdrożenia można śledzić na karcie **Dzienniki.**
   
# <a name="local-git"></a>[Lokalny system Git](#tab/local)

Zobacz [Lokalne wdrożenie usługi Git, aby Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure Repos](#tab/repos)

> [!NOTE]
> Azure Repos jako źródło wdrożenia jest obsługa aplikacji systemu Windows.
>

4. App Service kompilacji (Kudu) jest domyślnym dostawcą kompilacji.

    > [!NOTE]
    > Aby używać Azure Pipelines jako dostawcy kompilacji dla aplikacji App Service, nie konfiguruj go w App Service. Zamiast tego skonfiguruj ciszą/cd bezpośrednio z Azure Pipelines. Opcja **Azure Pipelines** wskazuje tylko właściwy kierunek.

1. Wybierz pozycję **Azure DevOps,** **Projekt,** **Repozytorium** i **Gałąź,** które chcesz wdrażać w sposób ciągły. 

    Jeśli Twoja organizacja DevOps nie znajduje się na liście, nie jest jeszcze połączona z Subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia z usługą platformy Azure.](/azure/devops/pipelines/library/connect-to-azure)

-----

## <a name="disable-continuous-deployment"></a>Wyłączanie ciągłego wdrażania

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania dla swojej App Service aplikacji.

1. W menu po lewej stronie kliknij pozycję **Ustawienia Centrum**  >  **wdrażania Rozłącz.**  >   

    ![Pokazuje, jak rozłączyć synchronizację folderu w chmurze z App Service aplikacji w Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Domyślnie plik GitHub Actions przepływu pracy jest zachowywany w repozytorium, ale nadal będzie wyzwalać wdrożenie w aplikacji. Aby usunąć go z repozytorium, wybierz pozycję **Usuń plik przepływu pracy.**

1. Kliknij przycisk **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Jak działa GitHub Actions kompilacji aplikacji

Dostawca GitHub Actions kompilacji jest opcją dla [ci/CD](#configure-deployment-source)z usługi GitHub i robi następujące czynności, aby skonfigurować ci/CD:

- Deponuje plik GitHub Actions przepływu pracy do repozytorium GitHub w celu obsługi zadań kompilacji i wdrażania w App Service.
- Dodaje profil publikowania dla aplikacji jako klucz tajny usługi GitHub. Plik przepływu pracy używa tego tajnego klucz do uwierzytelniania w App Service.
- Przechwytuje informacje z [dzienników uruchamiania przepływu](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) pracy i wyświetla je na karcie **Dzienniki** w Centrum **wdrażania aplikacji.**

Dostawcę kompilacji GitHub Actions dostosować w następujący sposób:

- Dostosuj plik przepływu pracy po jego wygenerowaniu w repozytorium GitHub. Aby uzyskać więcej informacji, zobacz [Składnia przepływu pracy dla GitHub Actions](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions). Upewnij się, że przepływ pracy jest wdrażany App Service użyciu [akcji azure/webapps-deploy.](https://github.com/Azure/webapps-deploy)
- Jeśli wybrana gałąź jest chroniona, nadal można wyświetlić podgląd pliku przepływu pracy bez zapisywania konfiguracji, a następnie ręcznie dodać go do repozytorium. Ta metoda nie daje integracji dziennika z Azure Portal.
- Zamiast profilu publikowania należy wdrożyć program przy użyciu [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) w Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Uwierzytelnianie za pomocą jednostki usługi

Ta opcjonalna konfiguracja zastępuje domyślne uwierzytelnianie profilami publikowania w wygenerowanym pliku przepływu pracy.

1. Wygeneruj jednostkę usługi za pomocą [polecenia az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) w interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) W poniższym przykładzie zastąp wartości *\<subscription-id>* *\<group-name>* , i *\<app-name>* własnymi wartościami:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Ze względów bezpieczeństwa przyznaj minimalny wymagany dostęp do jednostki usługi. Zakres w poprzednim przykładzie jest ograniczony do określonej App Service, a nie całej grupy zasobów.
    
1. Zapisz wszystkie dane wyjściowe JSON w następnym kroku, w tym dane najwyższego poziomu `{}` .

1. W [witrynie GitHub](https://github.com/)przejrzyj repozytorium i wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

1. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości w kluczu tajnym. Nadaj kluczowi tajnego nazwę, na przykład `AZURE_CREDENTIALS` .

1. W pliku przepływu pracy wygenerowanym przez Centrum wdrażania zmodyfikuj krok za pomocą kodu podobnego do poniższego przykładu (który jest modyfikowany z Node.js `azure/webapps-deploy` przepływu pracy):

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

W przypadku aplikacji systemu Windows można ręcznie skonfigurować ciągłe wdrażanie z repozytorium Git lub Mercurial w chmurze, które nie jest obsługiwane bezpośrednio przez portal, na przykład [GitLab.](https://gitlab.com/) W tym celu wybierz opcję Zewnętrzne repozytorium Git na **liście rozwijanej** Źródło. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego wdrażania przy użyciu kroków ręcznych.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="more-resources"></a>Więcej zasobów

* [Wdrażanie z Azure Pipelines na platformie Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Badanie typowych problemów z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Korzystanie z programu Azure PowerShell](/powershell/azure/)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
