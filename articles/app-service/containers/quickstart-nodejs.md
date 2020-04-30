---
title: 'Szybki Start: Tworzenie aplikacji node. js w systemie Linux'
description: Zacznij korzystać z aplikacji systemu Linux na Azure App Service, wdrażając pierwszą aplikację Node. js w kontenerze systemu Linux w App Service.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.devlang: javascript
ms.openlocfilehash: 24b93e9722ff399330b6843f065f183a3b1f5181
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202563"
---
# <a name="create-a-nodejs-app-in-azure"></a>Tworzenie aplikacji w języku Node. js na platformie Azure

Usługa Azure App Service oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji Node.js w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz konta platformy Azure, [zarejestruj się już dziś](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) , aby skorzystać z bezpłatnego konta z $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.

Wymagane [Visual Studio Code](https://code.visualstudio.com/) zainstalowane wraz z [Node. js i npm](https://nodejs.org/en/download), Menedżer pakietów środowiska Node. js.

Konieczne będzie również zainstalowanie [rozszerzenia Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), za pomocą którego można tworzyć i wdrażać Web Apps systemu Linux na platformie Azure jako usługa (PaaS) oraz zarządzać nimi.

### <a name="sign-in"></a>Zaloguj się

Po zainstalowaniu rozszerzenia Zaloguj się do konta platformy Azure. Na pasku działania wybierz logo platformy Azure, aby wyświetlić Eksplorator **usługi Azure App Service** . Wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami.

![Zaloguj się do platformy Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"**, może to być spowodowane faktem, że jesteś za serwerem proxy i nie można skontaktować się z interfejsem API platformy Azure. Skonfiguruj `HTTP_PROXY` i `HTTPS_PROXY` zmienne środowiskowe za pomocą informacji o serwerze proxy w `export`terminalu przy użyciu.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Jeśli ustawienie zmiennych środowiskowych nie rozwiąże problemu, skontaktuj się z nami, wybierając poniższy przycisk **został uruchomiony na przycisku problemu** .

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Przed kontynuowaniem upewnij się, że wszystkie wymagania wstępne zostały zainstalowane i skonfigurowane.

W VS Code adres e-mail platformy Azure powinien zostać wyświetlony na pasku stanu i w ramach subskrypcji w Eksploratorze **usługi Azure App Service** .

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Tworzenie aplikacji node. js

Następnie Utwórz aplikację Node. js, którą można wdrożyć w chmurze. Ten przewodnik Szybki Start używa generatora aplikacji w celu szybkiego tworzenia szkieletu aplikacji z poziomu terminalu.

> [!TIP]
> Jeśli zakończysz już [samouczek środowiska Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), możesz przejść do wdrożenia na [platformie Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Tworzenie szkieletu nowej aplikacji przy użyciu generatora ekspresowego

[Express](https://www.expressjs.com) to popularna platforma do kompilowania i uruchamiania aplikacji node. js. Można tworzyć szkieletowo nowe aplikacje ekspresowe przy użyciu narzędzia [Generator ekspres](https://expressjs.com/en/starter/generator.html) . Generator ekspresowy jest dostarczany jako moduł npm i może być uruchamiany bezpośrednio (bez instalacji) za pomocą narzędzia `npx`wiersza polecenia npm.

```bash
npx express-generator myExpressApp --view pug --git
```

Parametry informują generatora, aby używał aparatu szablonów Pug (dawniej znany `jade`jako) i aby utworzyć `.gitignore` plik. [pug](https://pugjs.org/api/getting-started.html) `--view pug --git`

Aby zainstalować wszystkie zależności aplikacji, przejdź do nowego folderu i uruchom `npm install`polecenie.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Następnie upewnij się, że aplikacja działa. W terminalu uruchom aplikację za pomocą polecenia, `npm start` aby uruchomić serwer.

```bash
npm start
```

Teraz otwórz przeglądarkę i przejdź do lokalizacji, `http://localhost:3000`w której powinny zostać wyświetlone następujące elementy:

![Uruchamianie aplikacji Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tej sekcji wdrażasz aplikację Node. js przy użyciu VS Code i Azure App Service rozszerzenia. W tym przewodniku Szybki Start jest używany Najpopularniejszy model wdrażania, w którym aplikacja jest spakowana i wdrażana w Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Wdróż przy użyciu Azure App Service

Najpierw otwórz folder aplikacji w VS Code.

```bash
code .
```

W Eksploratorze **usługi Azure App Service** wybierz ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure.

![Wdróż w aplikacji sieci Web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Możesz również wdrożyć z **palety poleceń** (Ctrl + Shift + P), wpisując polecenie "Deploy to Web App" i uruchamiając **Azure App Service: Deploy to Web App** .

1. Wybierz katalog, `myExpressApp`który jest aktualnie otwarty.

1. Wybierz pozycję **Utwórz nową aplikację sieci Web**, która domyślnie wdraża App Service w systemie Linux.

1. Wpisz globalnie unikatową nazwę aplikacji sieci Web, a następnie naciśnij klawisz ENTER. Prawidłowymi znakami dla nazwy aplikacji są "a-z", "0-9" i "-".

1. Wybierz **wersję środowiska Node. js**, zaleca się LTS.

    Kanał powiadomień przedstawia zasoby platformy Azure, które są tworzone dla aplikacji.

1. Wybierz opcję **tak** po wyświetleniu monitu, aby `npm install` zaktualizować konfigurację do uruchomienia na serwerze docelowym. Aplikacja zostanie wdrożona.

    ![Skonfigurowane wdrożenie](./media/quickstart-nodejs/server-build.png)

1. Po uruchomieniu wdrożenia zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, tak aby późniejsze wdrożenia były automatycznie ukierunkowane na tę samą App Service aplikacji sieci Web. Wybierz opcję **tak** , aby upewnić się, że zmiany zostaną wdrożone w odpowiedniej aplikacji.

    ![Skonfigurowane wdrożenie](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Upewnij się, że aplikacja nasłuchuje na porcie dostarczonym przez zmienną środowiskową portów `process.env.PORT`:.

### <a name="browse-the-app-in-azure"></a>Przeglądanie aplikacji na platformie Azure

Po zakończeniu wdrażania wybierz pozycję **Przeglądaj witrynę sieci** Web w wierszu polecenia, aby wyświetlić świeżą wdrożoną aplikację sieci Web.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie masz uprawnień do wyświetlenia tego katalogu lub strony"**, uruchomienie aplikacji prawdopodobnie nie powiodło się. Przejdź do następnej sekcji i Przejrzyj dane wyjściowe dziennika, aby znaleźć i naprawić błąd. Jeśli nie możesz rozwiązać tego problemu, skontaktuj się z nami, wybierając Poniższy **problem** . Mamy przyjemność pomóc!

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Zmiany w tej aplikacji można wdrożyć przy użyciu tego samego procesu i wybierając istniejącą aplikację zamiast tworzenia nowej.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

W tej sekcji dowiesz się, jak wyświetlać dzienniki (lub "ogon") z działającej aplikacji App Service. Wszystkie wywołania `console.log` w aplikacji są wyświetlane w oknie dane wyjściowe w Visual Studio Code.

Znajdź aplikację w Eksploratorze **usługi Azure App Service** , kliknij prawym przyciskiem myszy aplikację, a następnie wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

Po wyświetleniu monitu wybierz włączenie rejestrowania i ponownie uruchom aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno dane wyjściowe VS Code z połączeniem ze strumieniem dziennika.

![Wyświetlanie dzienników przesyłania strumieniowego](./media/quickstart-nodejs/view-logs.png)

![Włącz rejestrowanie i ponowne uruchamianie](./media/quickstart-nodejs/enable-restart.png)

Po kilku sekundach zobaczysz komunikat informujący o tym, że masz połączenie z usługą przesyłania strumieniowego dziennika. Odśwież stronę kilka razy, aby zobaczyć więcej działań.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się ukończyć ten przewodnik Szybki Start.

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub uzyskać wszystkie te elementy, instalując [pakiet Node Pack dla platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
