---
title: 'Szybki start: tworzenie aplikacji Linux Node.js'
description: Rozpocznij pracę z aplikacjami systemu Linux w usłudze Azure App Service, wdrażając pierwszą aplikację Node.js w kontenerze systemu Linux w usłudze App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 08a246b07effb8d5f3e2473a4d7959882cf43235
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687935"
---
# <a name="create-a-nodejs-app-in-azure"></a>Tworzenie aplikacji Node.js na platformie Azure

Usługa Azure App Service oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji Node.js w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz konta platformy Azure, [zarejestruj się już dziś, aby](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) uzyskać bezpłatne konto z kredytami o wartości $200 w kredytach platformy Azure, aby wypróbować dowolną kombinację usług.

Potrzebny jest [zainstalowany kod programu Visual Studio](https://code.visualstudio.com/) wraz z [node.js i npm](https://nodejs.org/en/download), menedżerem pakietów Node.js.

Należy również zainstalować [rozszerzenie usługi Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), którego można użyć do tworzenia, zarządzania i wdrażania aplikacji sieci Web systemu Linux na platformie Azure Platform as a Service (PaaS).

### <a name="sign-in"></a>Logowanie

Po zainstalowaniu rozszerzenia zaloguj się do konta platformy Azure. Na pasku aktywności wybierz logo platformy Azure, aby wyświetlić eksploratora **usługi AZURE APP SERVICE.** Wybierz **pozycję Zaloguj się na platformie Azure...** i postępuj zgodnie z instrukcjami.

![zaloguj się na platformie Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony błąd **"Nie można znaleźć subskrypcji o nazwie [identyfikator subskrypcji]"**, może to być spowodowane tym, że jesteś za serwerem proxy i nie możesz uzyskać dostępu do interfejsu API platformy Azure. Konfigurowanie `HTTP_PROXY` `HTTPS_PROXY` zmiennych środowiskowych i zmiennych środowiskowych `export`z informacjami proxy w terminalu za pomocą .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Jeśli ustawienie zmiennych środowiskowych nie rozwiąże problemu, skontaktuj się z nami, wybierając przycisk **Poniżej napotkałem problem.**

### <a name="prerequisite-check"></a>Sprawdzanie pod warunkiem

Przed kontynuowaniem upewnij się, że masz zainstalowane i skonfigurowane wszystkie wymagania wstępne.

W programie VS Code powinien zostać wyświetlony twój adres e-mail platformy Azure na pasku stanu i subskrypcji w Eksploratorze **usługi AZURE APP SERVICE.**

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Tworzenie aplikacji Node.js

Następnie należy utworzyć aplikację Node.js, którą można wdrożyć w chmurze. Ten szybki start używa generatora aplikacji, aby szybko uszkować aplikację z terminala.

> [!TIP]
> Jeśli ukończono już [samouczek Node.js,](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)możesz przejść do [wdrożenia na platformie Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Rusztowanie nowej aplikacji z generatorem ekspresowym

[Express](https://www.expressjs.com) jest popularną platformą do tworzenia i uruchamiania aplikacji Node.js. Można utworzyć (utworzyć) nową aplikację Express za pomocą narzędzia [Express Generator.](https://expressjs.com/en/starter/generator.html) Generator ekspresowy jest dostarczany jako moduł npm i może być uruchamiany bezpośrednio (bez `npx`instalacji) za pomocą narzędzia wiersza polecenia npm .

```bash
npx express-generator myExpressApp --view pug --git
```

Parametry `--view pug --git` mówią generatorowi, aby używał silnika [szablonu mopsa](https://pugjs.org/api/getting-started.html) (dawniej znanego jako) `jade`i tworzył `.gitignore` plik.

Aby zainstalować wszystkie zależności aplikacji, przejdź do nowego folderu i uruchom program `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Następnie upewnij się, że aplikacja jest uruchamiana. Z terminala uruchom aplikację `npm start` za pomocą polecenia, aby uruchomić serwer.

```bash
npm start
```

Teraz otwórz przeglądarkę i [http://localhost:3000](http://localhost:3000)przejdź do , gdzie powinieneś zobaczyć coś takiego:

![Uruchamianie aplikacji Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tej sekcji można wdrożyć aplikację Node.js przy użyciu programu VS Code i rozszerzenia usługi Azure App Service. Ten przewodnik Szybki start używa najbardziej podstawowego modelu wdrażania, w którym aplikacja jest spakowana i wdrożona w aplikacji Azure Web App w systemie Linux.

### <a name="deploy-using-azure-app-service"></a>Wdrażanie przy użyciu usługi Azure App Service

Najpierw otwórz folder aplikacji w programie VS Code.

```bash
code .
```

W Eksploratorze **USŁUGI APLIKACJI AZURE** wybierz niebieską ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure.

![Wdrażanie w aplikacji sieci Web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Można również wdrożyć z **palety poleceń** (CTRL + SHIFT + P), wpisując "wdrażanie w aplikacji sieci web" i uruchamianie usługi **Azure App Service: Wdrażanie w aplikacji Sieci Web** polecenia.

1. Wybierz katalog, który jest aktualnie `myExpressApp`otwarty, .

1. Wybierz **pozycję Utwórz nową aplikację sieci Web**, która domyślnie jest wdrażana w usłudze App Service w systemie Linux.

1. Wpisz globalnie unikatową nazwę aplikacji sieci Web i naciśnij klawisz ENTER. Prawidłowe znaki dla nazwy aplikacji to 'a-z', '0-9', i '-'.

1. Wybierz **wersję Node.js**, LTS jest zalecane.

    Kanał powiadomień pokazuje zasoby platformy Azure, które są tworzone dla aplikacji.

1. Wybierz **pozycję Tak** po wyświetleniu `npm install` monitu o zaktualizowanie konfiguracji do uruchomienia na serwerze docelowym. Aplikacja jest następnie wdrażana.

    ![Skonfigurowane wdrażanie](./media/quickstart-nodejs/server-build.png)

1. Po uruchomieniu wdrożenia zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, aby późniejsze wdrożenia były automatycznie kierowane na tę samą aplikację sieci Web usługi App Service. Wybierz **pozycję Tak,** aby upewnić się, że zmiany zostały wdrożone w odpowiedniej aplikacji.

    ![Skonfigurowane wdrażanie](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Upewnij się, że aplikacja nasłuchuje na porcie `process.env.PORT`dostarczonym przez zmienną środowiskową PORT: .

### <a name="browse-the-app-in-azure"></a>Przeglądanie aplikacji na platformie Azure

Po zakończeniu wdrażania wybierz **pozycję Przeglądaj** witrynę sieci Web w wierszu polecenia wyświetlenia świeżo wdrożonej aplikacji sieci web.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony błąd **"Nie masz uprawnień do wyświetlania tego katalogu lub strony."**, aplikacja prawdopodobnie nie może uruchomić poprawnie. Przejdź do następnej sekcji i wyświetl dane wyjściowe dziennika, aby znaleźć i naprawić błąd. Jeśli nie możesz go naprawić, skontaktuj się z nami, wybierając przycisk **Poniżej napotkałem problem.** Chętnie pomożemy!

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Zmiany w tej aplikacji można wdrożyć przy użyciu tego samego procesu i wybierając istniejącą aplikację, a nie tworząc nową.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

W tej sekcji dowiesz się, jak wyświetlić (lub "ogon") dzienniki z uruchomionej aplikacji usługi App Service. Wszystkie wywołania `console.log` w aplikacji są wyświetlane w oknie danych wyjściowych w programie Visual Studio Code.

Znajdź aplikację w Eksploratorze **USŁUGI APLIKACJI AZURE,** kliknij ją prawym przyciskiem myszy i wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

Po wyświetleniu monitu wybierz opcję włączenia rejestrowania i uruchom ponownie aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno wyjściowe kodu VS z połączeniem ze strumieniem dziennika.

![Wyświetlanie dzienników przesyłania strumieniowego](./media/quickstart-nodejs/view-logs.png)

![Włączanie rejestrowania i ponownego uruchamiania](./media/quickstart-nodejs/enable-restart.png)

Po kilku sekundach zostanie wyświetlony komunikat informujący, że masz połączenie z usługą przesyłania strumieniowego dziennika. Odśwież stronę kilka razy, aby zobaczyć więcej aktywności.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Następne kroki

Gratulacje, pomyślnie ukończyłeś ten szybki start!

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Możesz też uzyskać je wszystkie, instalując pakiet [node pack dla platformy Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
