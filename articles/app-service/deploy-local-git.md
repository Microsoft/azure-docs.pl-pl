---
title: Wdrażanie z lokalnego repozytorium Git
description: Dowiedz się, jak włączyć lokalne wdrożenie usługi Git na Azure App Service. Jeden z najprostszych sposobów wdrażania kodu z komputera lokalnego.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3196233728bb7f6493bbc06234c62d261ac99254
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832360"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokalne wdrożenie usługi Git w Azure App Service

W tym przewodniku przedstawiono sposób wdrażania aplikacji w [usłudze Azure App Service](overview.md) z repozytorium Git na komputerze lokalnym.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Zainstaluj program Git](https://www.git-scm.com/downloads).

- Masz lokalne repozytorium Git z kodem, który chcesz wdrożyć. Aby pobrać przykładowe repozytorium, uruchom następujące polecenie w lokalnym oknie terminalu:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

Zobacz [Konfigurowanie poświadczeń wdrożenia dla Azure App Service](deploy-configure-credentials.md). Możesz użyć poświadczeń zakresu użytkownika lub poświadczeń zakresu aplikacji.

## <a name="create-a-git-enabled-app"></a>Tworzenie aplikacji z obsługą usługi Git

Jeśli masz już aplikację App Service i chcesz skonfigurować dla niego lokalne wdrożenie usługi Git, zobacz [Konfigurowanie istniejącej](#configure-an-existing-app) aplikacji.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom [`az webapp create`](/cli/azure/webapp#az_webapp_create) z `--deployment-local-git` opcją . Na przykład:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Dane wyjściowe zawierają adres URL, taki jak `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` : . Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom [polecenia New-AzWebApp](/powershell/module/az.websites/new-azwebapp) z katalogu głównego repozytorium Git. Na przykład:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Uruchomienie tego polecenia cmdlet z katalogu, który jest repozytorium Git, powoduje automatyczne utworzenie zdalnego repozytorium Git dla aplikacji App Service o nazwie `azure` .

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

W portalu należy najpierw utworzyć aplikację, a następnie skonfigurować jej wdrożenie. Zobacz [Konfigurowanie istniejącej aplikacji.](#configure-an-existing-app)

-----

## <a name="configure-an-existing-app"></a>Konfigurowanie istniejącej aplikacji

Jeśli aplikacja nie została jeszcze utworzona, zobacz Tworzenie aplikacji z obsługą [usługi Git.](#create-a-git-enabled-app)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) . Na przykład:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Dane wyjściowe zawierają adres URL, taki jak `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` : . Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

> [!TIP]
> Ten adres URL zawiera nazwę użytkownika wdrożenia w zakresie użytkownika. Jeśli chcesz, możesz zamiast tego użyć [poświadczeń zakresu](deploy-configure-credentials.md#appscope) aplikacji. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Ustaw obiekt `scmType` aplikacji, uruchamiając polecenie cmdlet [Set-AzResource.](/powershell/module/az.resources/set-azresource)

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacją.

1. W menu po lewej stronie wybierz pozycję **Ustawienia Centrum**  >  **wdrażania.** Wybierz **pozycję Lokalne repozytorium Git** w **źródle,** a następnie kliknij pozycję **Zapisz.**

    ![Pokazuje, jak włączyć lokalne wdrożenie usługi Git dla App Service w Azure Portal](./media/deploy-local-git/enable-portal.png)

1. W sekcji Lokalne repozytorium Git skopiuj **Git Clone URI** na później. Ten URI nie zawiera żadnych poświadczeń.

-----

## <a name="deploy-the-web-app"></a>Wdrażanie aplikacji internetowej

1. W lokalnym oknie terminalu zmień katalog na katalog główny repozytorium Git, a następnie dodaj repozytorium zdalne Git przy użyciu adresu URL, który został pozyny z aplikacji. Jeśli wybrana metoda nie zawiera adresu URL, użyj metody `https://<app-name>.scm.azurewebsites.net/<app-name>.git` z nazwą aplikacji w pliku `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Jeśli [utworzono aplikację z obsługą usługi Git w programie PowerShell przy użyciu polecenia New-AzWebApp,](#create-a-git-enabled-app)repozytorium zdalne zostało już utworzone.
   
1. Wypchń na zdalną platformę Azure za pomocą . `git push azure master` 
   
1. W **oknie Menedżer poświadczeń** Git wprowadź poświadczenia zakresu użytkownika lub zakresu [aplikacji,](#configure-a-deployment-user)a nie poświadczenia logowania platformy Azure.

    Jeśli zdalny adres URL usługi Git zawiera już nazwę użytkownika i hasło, nie zostanie wyświetlony monit. 
   
1. Przejrzyj dane wyjściowe. Możesz zobaczyć automatyzację specyficzną dla środowiska uruchomieniowego, taką jak MSBuild dla środowiska ASP.NET, dla Node.js `npm install` i `pip install` dla języka Python. 
   
1. Przejdź do aplikacji na stronie Azure Portal, aby sprawdzić, czy zawartość została wdrożona.

## <a name="troubleshoot-deployment"></a>Rozwiązywanie problemów z wdrażaniem

Podczas publikowania w aplikacji usługi Git do publikowania w aplikacji App Service na platformie Azure mogą pojawić się następujące typowe komunikaty o błędach:

|Komunikat|Przyczyna|Rozwiązanie
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikacja nie jest uruchomiona.|Uruchom aplikację w Azure Portal. Wdrożenie usługi Git nie jest dostępne, gdy aplikacja internetowa zostanie zatrzymana.|
|`Couldn't resolve host 'hostname'`|Informacje o adresie zdalnym "azure" są nieprawidłowe.|Użyj polecenia `git remote -v` , aby wyświetlić listę wszystkich zdalnych elementów wraz ze skojarzonym adresem URL. Sprawdź, czy adres URL zdalnego adresu "azure" jest poprawny. W razie potrzeby usuń i utwórz ponownie ten zdalny przy użyciu poprawnego adresu URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nie określono gałęzi w ciągu lub nie `git push` ustawiono `push.default` wartości w . `.gitconfig`|Uruchom `git push` ponownie, określając główną gałąź: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|Wypchnięliśmy lokalną gałąź, która nie pasuje do gałęzi wdrożenia aplikacji na platformie "azure".|Sprawdź, czy bieżąca gałąź to `master` . Aby zmienić gałąź domyślną, użyj `DEPLOYMENT_BRANCH` ustawienia aplikacji.|
|`src refspec [branchname] does not match any.`|Próbowano wypchnąć do gałęzi innej niż main na zdalnym komputerze "azure".|Uruchom `git push` ponownie, określając główną gałąź: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ten błąd może wystąpić, jeśli spróbujesz wypchnąć duże repozytorium Git za pośrednictwem protokołu HTTPS.|Zmień konfigurację usługi git na komputerze lokalnym, aby była `postBuffer` większa. Na przykład: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Wdrożono aplikację Node.js z plikiem _package.js_ który określa dodatkowe wymagane moduły.|Przejrzyj komunikaty `npm ERR!` o błędach przed tym błędem, aby uzyskać więcej informacji o kontekście błędu. Poniżej przedstawiono znane przyczyny tego błędu i odpowiednie `npm ERR!` komunikaty:<br /><br />**Źle sformułowane package.jspliku**: `npm ERR! Couldn't read dependencies.`<br /><br />**Moduł macierzysty nie ma dystrybucji binarnej dla systemu Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />lub <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Dodatkowe zasoby

- [App Service kompilacji (dokumentacja projektu Kudu)](https://github.com/projectkudu/kudu/wiki)
- [Ciągłe wdrażanie do usługi Azure App Service](deploy-continuous-deployment.md)
- [Przykład: tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git (interfejs wiersza polecenia platformy Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Przykład: tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
