---
title: Wdróż z lokalnego repozytorium git
description: Dowiedz się, jak włączyć wdrożenie lokalnego narzędzia Git w Azure App Service. Jednym z najprostszych metod wdrażania kodu z komputera lokalnego.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5ed3e858168ce5ad9a7f089b723bb75ca8a49fca
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007521"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokalne wdrożenie narzędzia Git do Azure App Service

Ten przewodnik zawiera informacje na temat sposobu wdrażania aplikacji w celu [Azure App Service](overview.md) z repozytorium Git na komputerze lokalnym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Zainstaluj narzędzie git](https://www.git-scm.com/downloads).
  
- Posiadanie lokalnego repozytorium git z kodem, który chcesz wdrożyć. Aby pobrać przykładowe repozytorium, uruchom następujące polecenie w lokalnym oknie terminala:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-with-kudu-build-server"></a>Wdróż z serwerem kompilacji kudu

Najprostszym sposobem włączenia lokalnego wdrożenia narzędzia Git dla aplikacji przy użyciu serwera kudu App Service Build jest użycie Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Pobierz adres URL wdrożenia

Aby uzyskać adres URL umożliwiający lokalne wdrożenie narzędzia Git dla istniejącej aplikacji, uruchom polecenie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) w Cloud Shell. Zastąp \<app-name> \<group-name> wartości i nazwami aplikacji oraz jej grupą zasobów platformy Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Jeśli używasz planu usługi App-Service-system dla systemu Linux, musisz dodać ten parametr:--Runtime Python | 3.7


Lub, aby utworzyć nową aplikację z obsługą git, uruchom polecenie [`az webapp create`](/cli/azure/webapp#az-webapp-create) w Cloud Shell z `--deployment-local-git` parametrem. Zastąp \<app-name> , \<group-name> i \<plan-name> z nazwami nowej aplikacji git, jej grupą zasobów platformy Azure i planem Azure App Service.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Każde polecenie zwraca adres URL, na przykład: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

Zamiast używać tego adresu URL na poziomie konta, można również włączyć lokalny git przy użyciu poświadczeń na poziomie aplikacji. Azure App Service automatycznie generuje te poświadczenia dla każdej aplikacji. 

Pobierz poświadczenia aplikacji, uruchamiając następujące polecenie w Cloud Shell. Zastąp \<app-name> nazwy i nazwą \<group-name> swojej aplikacji oraz grupą zasobów platformy Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Użyj adresu URL, który powraca do wdrożenia aplikacji w następnym kroku.

### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web

1. Otwórz lokalne okno terminalu w lokalnym repozytorium git, a następnie Dodaj element zdalny platformy Azure. W poniższym poleceniu Zastąp \<url> ciąg adresem URL specyficznym dla użytkownika, który został uzyskany z poprzedniego kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Wypchnij do zdalnego systemu Azure za pomocą usługi `git push azure main` . 
   
1. W oknie **Menedżer poświadczeń git** wprowadź [hasło użytkownika wdrożenia](#configure-a-deployment-user), a nie hasło logowania do platformy Azure.
   
1. Przejrzyj dane wyjściowe. Dla `npm install` Node.js i dla języka Python może zostać wyświetlona Automatyzacja specyficzna dla środowiska uruchomieniowego, taka jak MSBuild for ASP.NET `pip install` . 
   
1. Przejdź do aplikacji w Azure Portal, aby sprawdzić, czy zawartość została wdrożona.

## <a name="deploy-with-azure-pipelines-builds"></a>Wdróż z kompilacjami Azure Pipelines

Jeśli Twoje konto ma wymagane uprawnienia, możesz skonfigurować Azure Pipelines (wersja zapoznawcza), aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji. 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisu w Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć rolę **właściciela** w ramach subskrypcji platformy Azure.

- Musisz być administratorem w projekcie usługi Azure DevOps, którego chcesz użyć.

Aby włączyć lokalne wdrożenie narzędzia Git dla aplikacji przy użyciu Azure Pipelines (wersja zapoznawcza):

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **App Services**. 

1. Wybierz aplikację Azure App Service i wybierz pozycję **centrum wdrażania** w menu po lewej stronie.
   
1. Na stronie **centrum wdrażania** wybierz pozycję **lokalny program git**, a następnie wybierz pozycję **Kontynuuj**. 
   
   ![Wybierz pozycję lokalny git, a następnie wybierz pozycję Kontynuuj.](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na stronie **dostawca kompilacji** wybierz pozycję **Azure Pipelines (wersja zapoznawcza)**, a następnie wybierz pozycję **Kontynuuj**. 
   
   ![Wybierz pozycję Azure Pipelines (wersja zapoznawcza), a następnie wybierz pozycję Kontynuuj.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na stronie **Konfigurowanie** Skonfiguruj nową organizację usługi Azure DevOps lub określ istniejącą organizację, a następnie wybierz pozycję **Kontynuuj**.
   
   > [!NOTE]
   > Jeśli istniejąca organizacja usługi Azure DevOps nie jest wymieniona, może być konieczne połączenie jej z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Definiowanie potoku wydania dysku CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. W zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/)planu App Service może zostać wyświetlona strona **wdrażanie do przemieszczania** . Zdecyduj, czy chcesz [włączyć miejsca wdrożenia](deploy-staging-slots.md), a następnie wybierz pozycję **Kontynuuj**.
   
1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ**.
   
1. Gdy potok platformy Azure jest gotowy, skopiuj adres URL repozytorium git ze strony **centrum wdrażania** , aby użyć go w następnym kroku. 
   
   ![Skopiuj adres URL repozytorium git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. W oknie terminalu lokalnego Dodaj zdalne Azure do lokalnego repozytorium git. W poleceniu Zastąp ciąg \<url> adresem URL repozytorium git uzyskanym w poprzednim kroku.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Wypchnij do zdalnego systemu Azure za pomocą usługi `git push azure main` . 
   
1. Na stronie **Menedżer poświadczeń git** Zaloguj się przy użyciu nazwy użytkownika VisualStudio.com. Aby poznać inne metody uwierzytelniania, zobacz [Omówienie uwierzytelniania Azure DevOps Services](/vsts/git/auth-overview?view=vsts).
   
1. Po zakończeniu wdrożenia Przejrzyj postęp kompilacji o godzinie `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` i postęp wdrażania o `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` .
   
1. Przejdź do aplikacji w Azure Portal, aby sprawdzić, czy zawartość została wdrożona.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Rozwiązywanie problemów z wdrażaniem

W przypadku publikowania w aplikacji App Service na platformie Azure przy użyciu narzędzia Git mogą pojawić się następujące typowe komunikaty o błędach:

|Wiadomość|Przyczyna|Rozwiązanie
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikacja nie działa.|Uruchom aplikację w Azure Portal. Wdrożenie narzędzia Git jest niedostępne, gdy aplikacja sieci Web jest zatrzymana.|
|`Couldn't resolve host 'hostname'`|Informacje o adresie dla elementu zdalnego "Azure" są nieprawidłowe.|Użyj `git remote -v` polecenia, aby wyświetlić listę wszystkich zdalnych, wraz z skojarzonym adresem URL. Sprawdź, czy adres URL dla zdalnego "Azure" jest prawidłowy. W razie konieczności Usuń i Utwórz ponownie ten zdalny przy użyciu poprawnego adresu URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nie określono gałęzi `git push` w trakcie lub wartość nie została ustawiona `push.default` w `.gitconfig` .|Uruchom `git push` ponownie, określając gałąź główną: `git push azure main` .|
|`src refspec [branchname] does not match any.`|Podjęto próbę wypchnięcia do gałęzi innej niż główna na pilocie "Azure".|Uruchom `git push` ponownie, określając gałąź główną: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ten błąd może wystąpić, jeśli spróbujesz wypchnąć duże repozytorium Git za pośrednictwem protokołu HTTPS.|Zmień konfigurację Git na komputerze lokalnym, aby zwiększyć `postBuffer` rozmiar. Przykład: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Wdrożono aplikację Node.js przy użyciu _package.jsw_ pliku, który określa dodatkowe wymagane moduły.|Sprawdź `npm ERR!` komunikaty o błędach przed tym błędem, aby uzyskać więcej kontekstu w przypadku awarii. Poniżej przedstawiono znane przyczyny tego błędu oraz odpowiadające im `npm ERR!` komunikaty:<br /><br />**Nieprawidłowo sformułowany package.jsw pliku**: `npm ERR! Couldn't read dependencies.`<br /><br />**Moduł macierzysty nie ma dystrybucji binarnej dla systemu Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />lub <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Dokumentacja programu Project kudu](https://github.com/projectkudu/kudu/wiki)
- [Ciągłe wdrażanie do usługi Azure App Service](deploy-continuous-deployment.md)
- [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium git (interfejs wiersza polecenia platformy Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
