---
title: 'Szybki Start: Tworzenie aplikacji sieci Web w języku PHP'
description: Wdróż swoje pierwsze Hello world w języku PHP do Azure App Service w ciągu kilku minut. Wdrażasz program przy użyciu narzędzia Git, który jest jednym z wielu sposobów wdrażania w App Service.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: f6876d0aef0d3d87e038b623c395f8368a14e90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97561855"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Tworzenie aplikacji sieci Web w języku PHP w Azure App Service

::: zone pivot="platform-windows"  
[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.  Ten samouczek szybki start przedstawia sposób wdrażania aplikacji PHP do Azure App Service w systemie Windows.
::: zone-end  

::: zone pivot="platform-linux"
[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.  Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji PHP w usłudze Azure App Service w systemie Linux.
::: zone-end  

Aplikacja internetowa zostanie utworzona przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) w usłudze Cloud Shell, a przykład kodu w języku PHP zostanie wdrożony w aplikacji internetowej za pomocą usługi Git.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-php/hello-world-in-browser.png)

Poniższe kroki możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux. Po zainstalowaniu wymagań wstępnych wykonanie czynności trwa około pięciu minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">Instalowanie języka PHP</a>

## <a name="download-the-sample-locally"></a>Pobieranie przykładu na maszynę lokalną

W oknie terminalu uruchom następujące polecenia. Spowoduje to sklonowanie aplikacji przykładowej na maszynę lokalną, a następnie przejście do katalogu zawierającego przykład kodu. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. Otwórz okno terminala i użyj polecenia `php` w celu uruchomienia wbudowanego serwera internetowego środowiska PHP.

```bash
php -S localhost:8080
```

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:8080`.

Na stronie zostanie wyświetlony komunikat **Hello World!** z przykładowej aplikacji.

![Przykładowa aplikacja działająca w środowisku lokalnym](media/quickstart-php/localhost-hello-world-in-browser.png)

W oknie terminalu naciśnij kombinację klawiszy **Ctrl + C**, aby zamknąć serwer sieci Web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W Cloud Shell Utwórz aplikację sieci Web w `myAppServicePlan` planie App Service za pomocą [`az webapp create`](/cli/azure/webapp#az-webapp-create) polecenia. 

W poniższym przykładzie zastąp ciąg `<app-name>` globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z`, `0-9` i `-`). Środowisko uruchomieniowe ma ustawioną wartość `PHP|7.4`. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, uruchom polecenie [`az webapp list-runtimes`](/cli/azure/webapp#az-webapp-list-runtimes) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> Symbol zatrzymania analizowania `(--%)` wprowadzony w programie powershell 3,0, nakazuje programowi PowerShell nieprzerwanie interpretacji danych wejściowych jako poleceń lub wyrażeń programu PowerShell.
>

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Utworzono pustą nową aplikację sieci Web z włączonym wdrożeniem usługi git.

> [!NOTE]
> Adres URL zdalnego repozytorium Git jest wyświetlany we właściwości `deploymentLocalGitUrl` w formacie `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Zapisz ten adres URL, ponieważ będzie on potrzebny później.
>

Przejdź do nowo utworzonej aplikacji internetowej. Zastąp _&lt; ciąg App-Name>_ unikatową nazwą aplikacji utworzoną w poprzednim kroku.

```bash
http://<app-name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Pusta strona aplikacji internetowej](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  main -> main
</pre>

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```
http://<app-name>.azurewebsites.net
```

Przykładowy kod w języku PHP jest uruchamiany w aplikacji internetowej usługi Azure App Service.

![Przykładowa aplikacja działająca na platformie Azure](media/quickstart-php/hello-world-in-browser.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku PHP w usłudze App Service.

## <a name="update-locally-and-redeploy-the-code"></a>Lokalne aktualizowanie i ponowne wdrażanie kodu

Za pomocą lokalnego edytora tekstów otwórz plik `index.php` w aplikacji w języku PHP i wprowadź niewielką zmianę w tekście ciągu obok polecenia `echo`:

```php
echo "Hello Azure!";
```

W oknie lokalnego terminala zatwierdź zmiany w usłudze Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git commit -am "updated output"
git push azure main
```

Po zakończeniu wdrożenia wróć do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie odśwież stronę.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

1. Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją internetową. Wyszukaj i wybierz **App Services**.

    ![Wyszukiwanie App Services, Azure Portal, tworzenie aplikacji sieci Web w języku PHP](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Wybierz nazwę aplikacji platformy Azure.

    ![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Zostanie wyświetlona strona **przeglądu** aplikacji sieci Web. W tym miejscu można wykonywać podstawowe zadania zarządzania, takie jak **przeglądanie**, **Zatrzymywanie**, **Ponowne uruchamianie** i **usuwanie**.

    ![Strona usługi App Service w witrynie Azure Portal](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    Menu aplikacji sieci Web zawiera różne opcje konfigurowania aplikacji. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [PHP z bazą danych MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji PHP](configure-language-php.md)
