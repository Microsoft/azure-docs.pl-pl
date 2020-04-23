---
title: 'Szybki start: tworzenie aplikacji sieci Web Node.js'
description: Wdrożenie pierwszego node.js Hello World w usłudze Azure App Service w ciągu kilku minut. Wdrażanie przy użyciu pakietu ZIP, który jest jednym z wielu sposobów wdrażania w usłudze App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: a185565ec125e327297883c63b1d50a2548c7853
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086009"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć aplikację w usłudze App Service w systemie _Linux_, zobacz [Tworzenie aplikacji internetowej środowiska Node.js w usłudze Azure App Service w systemie Linux](./containers/quickstart-nodejs.md).
>

[Usługa Azure App Service](overview.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingu.  Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji Node.js w usłudze Azure App Service. Aplikacja internetowa jest tworzona w usłudze [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), ale można te polecenia uruchomić również lokalnie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Przykładowy kod Node.js jest wdrażany w aplikacji internetowej przy użyciu polecenia [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

![Przykładowa aplikacja działająca na platformie Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

Poniższe kroki możesz wykonać przy użyciu komputera z systemem Mac, Windows lub Linux. Wykonanie tych kroków trwa około trzech minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Pobierz przykład

W usłudze Cloud Shell utwórz katalog Szybki start, a następnie przejdź do niego.

```console
mkdir quickstart

cd $HOME/quickstart
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```console
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

```output
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
```

> [!NOTE]
> Przykładowy plik index.js ustawia wartość process.env.PORT dla portu nasłuchiwania. Ta zmienna środowiskowa jest przypisana przez usługę App Service.
>

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

W aplikacji Cloud Shell utwórz `myAppServicePlan` aplikację internetową w [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) planie usługi app service za pomocą polecenia.

W poniższym przykładzie zastąp ciąg `<app_name>` globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z`, `0-9` i `-`).

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="set-nodejs-runtime"></a>Ustawianie środowiska uruchomieniowego platformy Node.js

Ustaw środowisko uruchomieniowe Node na wersję 10.14.1. Aby wyświetlić wszystkie obsługiwane środowiska [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)wykonawcze, uruchom plik .

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=10.14.1
```

Przejdź do nowo utworzonej aplikacji internetowej. Zastąp `<app_name>` unikatową nazwą aplikacji.

```http
http://<app_name>.azurewebsites.net
```

Oto, jak powinna wyglądać Twoja ![nowa aplikacja internetowa: Pusta strona aplikacji internetowej](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

## <a name="deploy-zip-file"></a>Wdrażanie plików ZIP

W usłudze Cloud Shell przejdź do katalogu głównego aplikacji i utwórz nowy plik ZIP dla przykładowego projektu.

```console
cd nodejs-docs-hello-world  

zip -r myUpdatedAppFiles.zip *.*
```

Wdróż plik ZIP do aplikacji internetowej przy użyciu polecenia [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację. Jeśli został skonfigurowany jakikolwiek dodatkowy niestandardowy proces kompilacji, również zostanie uruchomiony. Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji za pomocą przeglądarki sieci Web.

```http
http://<app_name>.azurewebsites.net
```

Przykładowy kod w języku Node.js jest uruchamiany w aplikacji internetowej usługi Azure App Service.

![Przykładowa aplikacja działająca na platformie Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

> [!NOTE]
> W usłudze Azure App Service aplikacja jest uruchamiana w usłudze IIS przy użyciu programu [iisnode](https://github.com/Azure/iisnode). Aby umożliwić uruchomienie aplikacji w programie iisnode, główny katalog aplikacji zawiera plik web.config. Plik jest możliwy do odczytu przez usługę IIS, a ustawienia związane z programem iisnode są udokumentowane w [repozytorium GitHub programu iisnode](https://github.com/Azure/iisnode/blob/master/src/samples/configuration/web.config).

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Node.js w usłudze App Service.

## <a name="update-and-redeploy-the-code"></a>Aktualizowanie i ponowne wdrażanie kodu

W usłudze Cloud Shell wpisz `code index.js`, aby otworzyć edytor usługi Cloud Shell.

![Plik index.js z kodem](media/app-service-web-get-started-nodejs-poc/code-indexjs.png)

Wprowadź niewielką zmianę w tekście w wywołaniu metody `response.end`:

```javascript
response.end("Hello Azure!");
```

Zapisz zmiany i zamknij edytor. Użyj polecenia `^S` w celu zapisania i polecenia `^Q` w celu zamknięcia programu.

Utwórz plik ZIP i wdróż go przy użyciu polecenia [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

```azurecli-interactive
# Bash
zip -r myUpdatedAppFiles.zip *.*

az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src myUpdatedAppFiles.zip
```

Przejdź z powrotem do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie odśwież stronę.

![Zaktualizowana przykładowa aplikacja działająca na platformie Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją internetową.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Strona usługi App Service w witrynie Azure Portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Środowisko Node.js z bazą danych MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
