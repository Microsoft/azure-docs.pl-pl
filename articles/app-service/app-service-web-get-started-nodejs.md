---
title: 'Szybki Start: Tworzenie aplikacji sieci Web w języku Node. js'
description: Wdróż pierwszy Hello world środowiska Node. js, aby Azure App Service w ciągu kilku minut. Wdrażasz za pomocą Visual Studio Code, który jest jednym z wielu sposobów wdrożenia do App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: df1e0809c0122eae593543cd193a6477b03d0938
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "82690795"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure 

Rozpocznij pracę z Azure App Service przez utworzenie aplikacji node. js/Express lokalnie przy użyciu Visual Studio Code, a następnie wdrożenie aplikacji w chmurze. Ponieważ korzystasz z bezpłatnej warstwy App Service, nie poniesiesz żadnych kosztów, aby ukończyć ten przewodnik Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node. js i npm](https://nodejs.org). Uruchom polecenie, `node --version` Aby sprawdzić, czy jest zainstalowany węzeł Node. js.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Azure App Service rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonowanie i uruchamianie lokalnej aplikacji node. js

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

W tej sekcji wdrażasz aplikację Node. js na platformie Azure przy użyciu VS Code i Azure App Service rozszerzenia.

1. Upewnij się, że jesteś w folderze *NodeJS-docs-Hello-World* , a następnie zacznij Visual Studio Code za pomocą następującego polecenia:

    ```bash
    code .
    ```

1. Na pasku działania VS Code Wybierz logo platformy Azure, aby wyświetlić Eksplorator **usługi Azure App Service** . Wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami. (Zobacz [Rozwiązywanie problemów z logowaniem do platformy Azure poniżej w](#troubleshooting-azure-sign-in) przypadku wystąpienia błędów). Po zalogowaniu się w Eksploratorze powinien zostać wyświetlona nazwa subskrypcji platformy Azure.

    ![Logowanie do platformy Azure](containers/media/quickstart-nodejs/sign-in.png)

1. W Eksploratorze **usługi Azure App SERVICE** vs Code wybierz ikonę niebieską Strzałka w górę, aby wdrożyć aplikację na platformie Azure. (Możesz również wywołać to samo polecenie w **palecie poleceń** (**Ctrl** + **SHIFT** + **P**), wpisując "Deploy to Web App" i wybierając **Azure App Service: Deploy to Web App**).

    ![Wdróż w aplikacji sieci Web](containers/media/quickstart-nodejs/deploy.png)
        
1. Wybierz folder *NodeJS-docs-Hello-World* .

1. Wybierz opcję tworzenia opartą na systemie operacyjnym, w którym chcesz wdrożyć:

    - Linux: wybierz pozycję **Utwórz nową aplikację sieci Web**
    - System Windows: wybierz pozycję **Utwórz nową aplikację sieci Web... Zaawansowane**

1. Wpisz globalnie unikatową nazwę aplikacji sieci Web, a następnie naciśnij klawisz **Enter**. Nazwa musi być unikatowa w całej platformie Azure i używać tylko znaków alfanumerycznych ("A-Z", "A-z" i "0-9") oraz łączników ("-").

1. W przypadku określania systemu Linux wybierz wersję środowiska Node. js po wyświetleniu monitu. Zalecana jest wersja **LTS** .

1. Jeśli celem jest system Windows, postępuj zgodnie z dodatkowymi monitami:
    1. Wybierz pozycję **Utwórz nową grupę zasobów**, a następnie wprowadź nazwę grupy zasobów, na przykład `AppServiceQS-rg` .
    1. Wybierz **system Windows** dla systemu operacyjnego.
    1. Wybierz pozycję **Utwórz nowy plan App Service**, a następnie wprowadź nazwę planu (na przykład `AppServiceQS-plan` ), a następnie wybierz pozycję **F1 bezpłatnie** dla warstwy cenowej.
    1. Wybierz pozycję **Pomiń teraz** po wyświetleniu monitu o Application Insights.
    1. Wybierz region w sąsiedztwie lub blisko zasobów, do których chcesz uzyskać dostęp.

1. Po udzieleniu odpowiedzi na wszystkie monity VS Code pokaże zasoby platformy Azure, które są tworzone dla aplikacji w oknie podręcznym powiadomienia.

    Podczas wdrażania w systemie Linux wybierz opcję **tak** po wyświetleniu monitu, aby zaktualizować konfigurację do uruchomienia `npm install` na docelowym serwerze z systemem Linux.

    ![Monituj o aktualizację konfiguracji na docelowym serwerze z systemem Linux](containers/media/quickstart-nodejs/server-build.png)

1. Wybierz opcję **tak** po wyświetleniu monitu **, aby zawsze wdrażać obszar roboczy "NodeJS-docs-Hello-World" do (nazwa aplikacji) "**. Wybranie pozycji **tak** informuje vs Code, aby automatycznie kierować tę samą aplikację App Service do aplikacji sieci Web z kolejnymi wdrożeniami.

1. W przypadku wdrażania w systemie Linux wybierz opcję **Przeglądaj witrynę internetową** w wierszu polecenia, aby wyświetlić świeżo wdrożoną aplikację sieci Web po zakończeniu wdrażania. W przeglądarce powinna być wyświetlana wartość "Hello world!"

1. W przypadku wdrażania w systemie Windows należy najpierw ustawić numer wersji środowiska Node. js dla aplikacji sieci Web:

    1. W VS Code rozwiń węzeł nowej usługi App Service, kliknij prawym przyciskiem myszy pozycję **Ustawienia aplikacji**, a następnie wybierz pozycję **Dodaj nowe ustawienie...**:

        ![Dodaj ustawienie aplikacji — polecenie](containers/media/quickstart-nodejs/add-setting.png)

    1. Wprowadź `WEBSITE_NODE_DEFAULT_VERSION` wartość w polu klucz ustawienia.
    1. Wprowadź `10.15.2` wartość ustawienia.
    1. Kliknij prawym przyciskiem myszy węzeł usługi App Service i wybierz polecenie **Uruchom ponownie**

        ![Uruchom ponownie usługę App Service](containers/media/quickstart-nodejs/restart.png)

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
](containers/media/quickstart-nodejs/view-logs.png)

1. Po wyświetleniu monitu wybierz włączenie rejestrowania i ponownie uruchom aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno dane wyjściowe VS Code z połączeniem ze strumieniem dziennika. 

    ![Włącz rejestrowanie i ponowne uruchamianie](containers/media/quickstart-nodejs/enable-restart.png)

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

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub uzyskać wszystkie te elementy, instalując [pakiet Node Pack dla platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
