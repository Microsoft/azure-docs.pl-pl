---
title: Wdróż z lokalnego repozytorium git
description: Dowiedz się, jak włączyć wdrożenie lokalnego narzędzia Git w Azure App Service. Jednym z najprostszych metod wdrażania kodu z komputera lokalnego.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5dd6183bf88c167adb2f084c319cd90b94351dfb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560468"
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

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

Zobacz [Konfigurowanie poświadczeń wdrażania dla Azure App Service](deploy-configure-credentials.md). Można użyć poświadczeń zakresu użytkownika lub poświadczeń zakresu aplikacji.

## <a name="create-a-git-enabled-app"></a>Tworzenie aplikacji z obsługą usługi git

Jeśli masz już aplikację App Service i chcesz skonfigurować lokalne wdrożenie narzędzia Git, zobacz sekcję [Konfigurowanie istniejącej aplikacji](#configure-an-existing-app) .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom [`az webapp create`](/cli/azure/webapp#az_webapp_create) polecenie z `--deployment-local-git` opcją. Na przykład:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

Dane wyjściowe zawierają adres URL, taki jak: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom narzędzie [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) z poziomu katalogu głównego repozytorium git. Na przykład:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Gdy uruchamiasz to polecenie cmdlet z katalogu, który jest repozytorium git, automatycznie tworzy zdalne narzędzie git dla aplikacji App Service, o nazwie `azure` .

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

W portalu należy najpierw utworzyć aplikację, a następnie skonfigurować dla niej wdrożenie. Zobacz [Konfigurowanie istniejącej aplikacji](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Konfigurowanie istniejącej aplikacji

Jeśli jeszcze nie utworzono aplikacji, zobacz sekcję [Tworzenie aplikacji z obsługą usługi git](#create-a-git-enabled-app) .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) . Na przykład:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Dane wyjściowe zawierają adres URL, taki jak: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Użyj tego adresu URL, aby wdrożyć aplikację w następnym kroku.

> [!TIP]
> Ten adres URL zawiera nazwę użytkownika wdrożenia zakresu User. Jeśli chcesz, możesz zamiast tego [użyć poświadczeń zakresu aplikacji](deploy-configure-credentials.md#appscope) . 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Ustaw `scmType` aplikację, uruchamiając polecenie cmdlet [Set-AzResource](/powershell/module/az.resources/set-azresource) .

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do strony zarządzania aplikacji.

1. W menu po lewej stronie wybierz pozycję Ustawienia **centrum wdrażania**  >  . Wybierz pozycję **lokalny git** w obszarze **Źródło**, a następnie kliknij przycisk **Zapisz**.

    ![Pokazuje, jak włączyć lokalne wdrożenie narzędzia Git dla App Service w Azure Portal](./media/deploy-local-git/enable-portal.png)

1. W lokalnej sekcji git Skopiuj **identyfikator Uri git clone** w późniejszym czasie. Ten identyfikator URI nie zawiera żadnych poświadczeń.

-----

## <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web

1. W oknie terminalu lokalnego Zmień katalog na katalog główny repozytorium git, a następnie Dodaj element zdalny git przy użyciu adresu URL, który został uzyskany z aplikacji. Jeśli wybrana metoda nie daje adresu URL, użyj `https://<app-name>.scm.azurewebsites.net/<app-name>.git` nazwy aplikacji w `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Jeśli [utworzono aplikację z obsługą usługi Git w programie PowerShell przy użyciu polecenia New-AzWebApp](#create-a-git-enabled-app), zdalne jest już utworzone.
   
1. Wypchnij do zdalnego systemu Azure za pomocą usługi `git push azure master` . 
   
1. W oknie **Menedżer poświadczeń usługi git** wprowadź poświadczenia zakresu [użytkownika lub zakresu aplikacji](#configure-a-deployment-user), a nie poświadczenia logowania do platformy Azure.

    Jeśli zdalny adres URL narzędzia Git zawiera już nazwę użytkownika i hasło, nie zostanie wyświetlony monit. 
   
1. Przejrzyj dane wyjściowe. Dla `npm install` Node.js i dla języka Python może zostać wyświetlona Automatyzacja specyficzna dla środowiska uruchomieniowego, taka jak MSBuild for ASP.NET `pip install` . 
   
1. Przejdź do aplikacji w Azure Portal, aby sprawdzić, czy zawartość została wdrożona.

## <a name="troubleshoot-deployment"></a>Rozwiązywanie problemów z wdrażaniem

W przypadku publikowania w aplikacji App Service na platformie Azure przy użyciu narzędzia Git mogą pojawić się następujące typowe komunikaty o błędach:

|Komunikat|Przyczyna|Rozwiązanie
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Aplikacja nie działa.|Uruchom aplikację w Azure Portal. Wdrożenie narzędzia Git jest niedostępne, gdy aplikacja sieci Web jest zatrzymana.|
|`Couldn't resolve host 'hostname'`|Informacje o adresie dla elementu zdalnego "Azure" są nieprawidłowe.|Użyj `git remote -v` polecenia, aby wyświetlić listę wszystkich zdalnych, wraz z skojarzonym adresem URL. Sprawdź, czy adres URL dla zdalnego "Azure" jest prawidłowy. W razie konieczności Usuń i Utwórz ponownie ten zdalny przy użyciu poprawnego adresu URL.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nie określono gałęzi `git push` w trakcie lub wartość nie została ustawiona `push.default` w `.gitconfig` .|Uruchom `git push` ponownie, określając gałąź główną: `git push azure main` .|
|`src refspec [branchname] does not match any.`|Podjęto próbę wypchnięcia do gałęzi innej niż główna na pilocie "Azure".|Uruchom `git push` ponownie, określając gałąź główną: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ten błąd może wystąpić, jeśli spróbujesz wypchnąć duże repozytorium Git za pośrednictwem protokołu HTTPS.|Zmień konfigurację Git na komputerze lokalnym, aby zwiększyć `postBuffer` rozmiar. Na przykład: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Wdrożono aplikację Node.js przy użyciu _package.jsw_ pliku, który określa dodatkowe wymagane moduły.|Sprawdź `npm ERR!` komunikaty o błędach przed tym błędem, aby uzyskać więcej kontekstu w przypadku awarii. Poniżej przedstawiono znane przyczyny tego błędu oraz odpowiadające im `npm ERR!` komunikaty:<br /><br />**Nieprawidłowo sformułowany package.jsw pliku**: `npm ERR! Couldn't read dependencies.`<br /><br />**Moduł macierzysty nie ma dystrybucji binarnej dla systemu Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />lub <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Serwer kompilacji App Service (dokumentacja projektu kudu)](https://github.com/projectkudu/kudu/wiki)
- [Ciągłe wdrażanie do usługi Azure App Service](deploy-continuous-deployment.md)
- [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium git (interfejs wiersza polecenia platformy Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Przykład: Tworzenie aplikacji internetowej i wdrażanie kodu z lokalnego repozytorium git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
