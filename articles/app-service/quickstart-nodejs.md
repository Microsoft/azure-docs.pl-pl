---
title: 'Szybki Start: Tworzenie aplikacji sieci Web Node.js'
description: Wdróż swoje pierwsze Node.js Hello world do Azure App Service w ciągu kilku minut.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 3b1ff02d6a9b3e8c7b7abaf1b185a9becd2be12b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105837"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure

::: zone pivot="platform-windows"  

Rozpocznij pracę z usługą Azure App Service, Node.jstworząc lokalnie aplikację/Express przy użyciu usługi Visual Studio Code i wdrażając aplikację w chmurze. Ponieważ korzystasz z bezpłatnej warstwy App Service, nie poniesiesz żadnych kosztów, aby ukończyć ten przewodnik Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
- [Node.js i npm](https://nodejs.org). Uruchom polecenie, `node --version` Aby sprawdzić, czy Node.js jest zainstalowana.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Azure App Service rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonowanie i uruchamianie lokalnej aplikacji Node.js

1. Na komputerze lokalnym Otwórz Terminal i Sklonuj przykładowe repozytorium:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Przejdź do nowego folderu aplikacji:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Uruchom aplikację, aby przetestować ją lokalnie:

    ```bash
    npm start
    ```
    
1. Otwórz przeglądarkę i przejdź do `http://localhost:1337` . W przeglądarce powinna zostać wyświetlona wartość "Hello world!".

1. Naciśnij klawisz **Ctrl** + **C** w terminalu, aby zatrzymać serwer.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tej sekcji wdrażasz aplikację Node.js na platformie Azure przy użyciu VS Code i rozszerzenia Azure App Service.

1. Upewnij się, że jesteś w folderze *NodeJS-docs-Hello-World* , a następnie zacznij Visual Studio Code za pomocą następującego polecenia:

    ```bash
    code .
    ```

1. Na pasku działania VS Code Wybierz logo platformy Azure, aby wyświetlić Eksplorator **usługi Azure App Service** . Wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami. (Zobacz [Rozwiązywanie problemów z logowaniem do platformy Azure poniżej w](#troubleshooting-azure-sign-in) przypadku wystąpienia błędów). Po zalogowaniu się w Eksploratorze powinien zostać wyświetlona nazwa subskrypcji platformy Azure.

    ![Logowanie do platformy Azure](media/quickstart-nodejs/sign-in.png)

1. W Eksploratorze **usługi Azure App SERVICE** vs Code wybierz ikonę niebieską Strzałka w górę, aby wdrożyć aplikację na platformie Azure. (Możesz również wywołać to samo polecenie w **palecie poleceń** (**Ctrl** + **SHIFT** + **P**), wpisując "Deploy to Web App" i wybierając **Azure App Service: Deploy to Web App**).

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Zrzut ekranu usługi Azure App Service w VS Code z wybraną ikoną niebieską strzałką.":::
        
1. Wybierz folder *NodeJS-docs-Hello-World* .

1. Wybierz opcję tworzenia opartą na systemie operacyjnym, w którym chcesz wdrożyć:

    - Linux: wybierz pozycję **Utwórz nową aplikację sieci Web**
    - System Windows: wybierz pozycję **Utwórz nową aplikację sieci Web... Zaawansowane**

1. Wpisz globalnie unikatową nazwę aplikacji sieci Web, a następnie naciśnij klawisz **Enter**. Nazwa musi być unikatowa w całej platformie Azure i używać tylko znaków alfanumerycznych ("A-Z", "A-z" i "0-9") oraz łączników ("-").

1. W przypadku określania systemu Linux wybierz wersję Node.js po wyświetleniu monitu. Zalecana jest wersja **LTS** .

1. Jeśli celem jest system Windows, postępuj zgodnie z dodatkowymi monitami:
    1. Wybierz pozycję **Utwórz nową grupę zasobów**, a następnie wprowadź nazwę grupy zasobów, na przykład `AppServiceQS-rg` .
    1. Wybierz **system Windows** dla systemu operacyjnego.
    1. Wybierz pozycję **Utwórz nowy plan App Service**, a następnie wprowadź nazwę planu (na przykład `AppServiceQS-plan` ), a następnie wybierz pozycję **F1 bezpłatnie** dla warstwy cenowej.
    1. Wybierz pozycję **Pomiń teraz** po wyświetleniu monitu o Application Insights.
    1. Wybierz region w sąsiedztwie lub blisko zasobów, do których chcesz uzyskać dostęp.

1. Po udzieleniu odpowiedzi na wszystkie monity VS Code pokaże zasoby platformy Azure, które są tworzone dla aplikacji w oknie podręcznym powiadomienia.

    Podczas wdrażania w systemie Linux wybierz opcję **tak** po wyświetleniu monitu, aby zaktualizować konfigurację do uruchomienia `npm install` na docelowym serwerze z systemem Linux.

    ![Monituj o aktualizację konfiguracji na docelowym serwerze z systemem Linux](media/quickstart-nodejs/server-build.png)

1. Wybierz opcję **tak** po wyświetleniu monitu **, aby zawsze wdrażać obszar roboczy "NodeJS-docs-Hello-World" do (nazwa aplikacji) "**. Wybranie pozycji **tak** informuje vs Code, aby automatycznie kierować tę samą aplikację App Service do aplikacji sieci Web z kolejnymi wdrożeniami.

1. W przypadku wdrażania w systemie Linux wybierz opcję **Przeglądaj witrynę internetową** w wierszu polecenia, aby wyświetlić świeżo wdrożoną aplikację sieci Web po zakończeniu wdrażania. W przeglądarce powinna być wyświetlana wartość "Hello world!"

1. W przypadku wdrażania w systemie Windows należy najpierw ustawić numer wersji Node.js dla aplikacji sieci Web:

    1. W VS Code rozwiń węzeł nowej usługi App Service, kliknij prawym przyciskiem myszy pozycję **Ustawienia aplikacji**, a następnie wybierz pozycję **Dodaj nowe ustawienie...**:

        ![Dodaj ustawienie aplikacji — polecenie](media/quickstart-nodejs/add-setting.png)

    1. Wprowadź `WEBSITE_NODE_DEFAULT_VERSION` wartość w polu klucz ustawienia.
    1. Wprowadź `10.15.2` wartość ustawienia.
    1. Kliknij prawym przyciskiem myszy węzeł usługi App Service i wybierz polecenie **Uruchom ponownie**

        ![Uruchom ponownie usługę App Service](media/quickstart-nodejs/restart.png)

    1. Kliknij prawym przyciskiem myszy węzeł usługi App Service, a następnie wybierz pozycję **Przeglądaj witrynę sieci Web**.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Rozwiązywanie problemów z logowaniem do platformy Azure

Jeśli zostanie wyświetlony komunikat o błędzie **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"** podczas logowania do platformy Azure, może to być spowodowane tym, że jesteś za serwerem proxy i nie można uzyskać dostępu do interfejsu API platformy Azure. Skonfiguruj `HTTP_PROXY` i `HTTPS_PROXY` zmienne środowiskowe za pomocą informacji o serwerze proxy w terminalu przy użyciu `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Jeśli ustawienie zmiennych środowiskowych nie rozwiąże problemu, skontaktuj się z nami, wybierając powyżej przycisk Rozwiąż **problem** .

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Zmiany w tej aplikacji można wdrożyć przez wprowadzanie zmian w VS Code, zapisywanie plików, a następnie używanie tego samego procesu jak przed wybraniem istniejącej aplikacji zamiast tworzenia nowej.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

Możesz wyświetlić dane wyjściowe dziennika (wywołania do `console.log` ) z aplikacji bezpośrednio w oknie danych wyjściowych vs Code.

1. W Eksploratorze **usługi Azure App Service** kliknij prawym przyciskiem myszy węzeł aplikacji, a następnie wybierz polecenie **Uruchom dzienniki przesyłania strumieniowego**.

    ![Uruchom przesyłanie strumieniowe dzienników
](media/quickstart-nodejs/view-logs.png)

1. Po wyświetleniu monitu wybierz włączenie rejestrowania i ponownie uruchom aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno dane wyjściowe VS Code z połączeniem ze strumieniem dziennika. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Zrzut ekranu przedstawiający monit Visual Studio Code o włączenie rejestrowania i ponowne uruchomienie aplikacji z wybranym przyciskiem tak.":::

1. Po kilku sekundach w oknie danych wyjściowych zostanie wyświetlony komunikat z informacją o tym, że masz połączenie z usługą przesyłania strumieniowego dziennika. Można generować więcej działań wyjściowych, odświeżając stronę w przeglądarce.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się ukończyć ten przewodnik Szybki Start.

> [!div class="nextstepaction"]
> [Samouczek: Node.js aplikacji za pomocą MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Node.js](configure-language-nodejs.md)

Zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub uzyskać wszystkie te elementy, instalując [pakiet Node Pack dla platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz konta platformy Azure, [zarejestruj się już dziś](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) , aby skorzystać z bezpłatnego konta z $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.

Musisz zainstalować [Visual Studio Code](https://code.visualstudio.com/) wraz z [Node.js i npm](https://nodejs.org/en/download), Menedżer pakietów Node.js.

Konieczne będzie również zainstalowanie [rozszerzenia Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), za pomocą którego można tworzyć i wdrażać Web Apps systemu Linux na platformie Azure jako usługa (PaaS) oraz zarządzać nimi.

### <a name="sign-in"></a>Zaloguj

Po zainstalowaniu rozszerzenia Zaloguj się do konta platformy Azure. Na pasku działania wybierz logo platformy Azure, aby wyświetlić Eksplorator **usługi Azure App Service** . Wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami.

![Zaloguj się do platformy Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"**, może to być spowodowane faktem, że jesteś za serwerem proxy i nie można skontaktować się z interfejsem API platformy Azure. Skonfiguruj `HTTP_PROXY` i `HTTPS_PROXY` zmienne środowiskowe za pomocą informacji o serwerze proxy w terminalu przy użyciu `export` .

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

## <a name="create-your-nodejs-application"></a>Tworzenie aplikacji Node.js

Następnie Utwórz aplikację Node.js, którą można wdrożyć w chmurze. Ten przewodnik Szybki Start używa generatora aplikacji w celu szybkiego tworzenia szkieletu aplikacji z poziomu terminalu.

> [!TIP]
> Jeśli już ukończysz [ samouczekNode.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), możesz przejść do [wdrożenia na platformie Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Tworzenie szkieletu nowej aplikacji przy użyciu generatora ekspresowego

[Express](https://www.expressjs.com) to popularna platforma do kompilowania i uruchamiania aplikacji Node.js. Można tworzyć szkieletowo nowe aplikacje ekspresowe przy użyciu narzędzia [Generator ekspres](https://expressjs.com/en/starter/generator.html) . Generator ekspresowy jest dostarczany jako moduł npm i może być uruchamiany bezpośrednio (bez instalacji) za pomocą narzędzia wiersza polecenia npm `npx` .

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git`Parametry informują generatora, aby używał aparatu szablonów [Pug](https://pugjs.org/api/getting-started.html) (dawniej znany jako `jade` ) i aby utworzyć `.gitignore` plik.

Aby zainstalować wszystkie zależności aplikacji, przejdź do nowego folderu i uruchom polecenie `npm install` .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Następnie upewnij się, że aplikacja działa. W terminalu uruchom aplikację za pomocą `npm start` polecenia, aby uruchomić serwer.

```bash
npm start
```

Teraz otwórz przeglądarkę i przejdź do `http://localhost:3000` lokalizacji, w której powinny zostać wyświetlone następujące elementy:

![Uruchamianie aplikacji Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

W tej sekcji wdrażasz aplikację Node.js przy użyciu VS Code i rozszerzenia Azure App Service. W tym przewodniku Szybki Start jest używany Najpopularniejszy model wdrażania, w którym aplikacja jest spakowana i wdrażana w Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Wdróż przy użyciu Azure App Service

Najpierw otwórz folder aplikacji w VS Code.

```bash
code .
```

W Eksploratorze **usługi Azure App Service** wybierz ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Zrzut ekranu przedstawiający menu Azure App Service w Visual Studio Code z wybraną niebieską strzałką Deploy.":::

> [!TIP]
> Możesz również wdrożyć z **palety poleceń** (Ctrl + Shift + P), wpisując polecenie "Deploy to Web App" i uruchamiając **Azure App Service: Deploy to Web App** .

1. Wybierz katalog, który jest aktualnie otwarty `myExpressApp` .

1. Wybierz pozycję **Utwórz nową aplikację sieci Web**, która domyślnie wdraża App Service w systemie Linux.

1. Wpisz globalnie unikatową nazwę aplikacji sieci Web, a następnie naciśnij klawisz ENTER. Prawidłowymi znakami dla nazwy aplikacji są "a-z", "0-9" i "-".

1. Wybierz **wersjęNode.js**, zalecamy LTS.

    Kanał powiadomień przedstawia zasoby platformy Azure, które są tworzone dla aplikacji.

1. Wybierz opcję **tak** po wyświetleniu monitu, aby zaktualizować konfigurację do uruchomienia `npm install` na serwerze docelowym. Aplikacja zostanie wdrożona.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Zrzut ekranu przedstawiający monit o zaktualizowanie konfiguracji na serwerze docelowym z wybranym przyciskiem tak.":::

1. Po uruchomieniu wdrożenia zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, tak aby późniejsze wdrożenia były automatycznie ukierunkowane na tę samą App Service aplikacji sieci Web. Wybierz opcję **tak** , aby upewnić się, że zmiany zostaną wdrożone w odpowiedniej aplikacji.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Zrzut ekranu przedstawiający monit o zaktualizowanie obszaru roboczego z wybranym przyciskiem tak.":::

> [!TIP]
> Upewnij się, że aplikacja nasłuchuje na porcie dostarczonym przez zmienną środowiskową portów: `process.env.PORT` .

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

Znajdź aplikację w Eksploratorze **usługi Azure App Service** , kliknij prawym przyciskiem myszy aplikację, a następnie wybierz polecenie **Uruchom dzienniki przesyłania strumieniowego**.

Zostanie otwarte okno dane wyjściowe VS Code z połączeniem ze strumieniem dziennika.

![Uruchom przesyłanie strumieniowe dzienników
](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Zrzut ekranu przedstawiający monit VS Code o włączenie rejestrowania plików i ponowne uruchomienie aplikacji sieci Web z wybranym przyciskiem tak.":::

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


::: zone-end
