---
title: 'Szybki Start: Tworzenie aplikacji sieci Web Node.js'
description: Wdróż swoje pierwsze Node.js Hello world do Azure App Service w ciągu kilku minut.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748606"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure

Wprowadzenie do <abbr title="Usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i aplikacji zaplecza mobilnego.">Azure App Service</abbr> Tworzenie aplikacji Node.js/Express lokalnie przy użyciu Visual Studio Code, a następnie wdrażanie aplikacji w chmurze platformy Azure. Ponieważ używasz <abbr title="W Azure App Service warstwa podstawowa, w której aplikacja działa na tych samych maszynach wirtualnych, co inne aplikacje, w tym aplikacje innych klientów. Ta warstwa jest przeznaczona do programowania i testowania.">warstwa Bezpłatna</abbr>nie ponosisz żadnych kosztów, aby ukończyć ten przewodnik Szybki Start.

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

- Konto platformy Azure z aktywnym <abbr title="Subskrypcja platformy Azure to kontener logiczny, który służy do aprowizowania zasobów na platformie Azure. Są w niej przechowywane szczegółowe informacje o wszystkich posiadanych zasobach, takich jak maszyny wirtualne, bazy danych i inne.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
- [Node.js i npm](https://nodejs.org). Uruchom polecenie, `node --version` Aby sprawdzić, czy Node.js jest zainstalowana.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Azure App Service rozszerzenie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Visual Studio Code.

[zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. klonowanie i uruchamianie lokalnej aplikacji Node.js

1. Na komputerze lokalnym Otwórz Terminal i Sklonuj przykładowe repozytorium:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Przejdź do nowego folderu aplikacji:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Zainstaluj zależności:

    ```bash
    npm install
    ```

1. Uruchom aplikację, aby przetestować ją lokalnie:

    ```bash
    npm start
    ```
    
1. Otwórz przeglądarkę i przejdź do `http://localhost:1337` . W przeglądarce powinna zostać wyświetlona wartość "Hello world!".

1. Naciśnij klawisz <kbd>Ctrl</kbd>  +  <kbd>C</kbd> w terminalu, aby zatrzymać serwer.

[zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Wyświetlanie dzienników z Visual Studio Code

Wyświetl <abbr title="Wszystkie wywołania `console.log` w aplikacji są wyświetlane w oknie dane wyjściowe w Visual Studio Code.">Dziennik</abbr> uruchomionej aplikacji App Service.

1. Znajdź aplikację w Eksploratorze **usługi Azure App Service** , kliknij prawym przyciskiem myszy nazwę aplikacji, a następnie wybierz polecenie **Uruchom dzienniki przesyłania strumieniowego**.

1. Zostanie otwarte okno dane wyjściowe Visual Studio Code.

    ![Wyświetlanie dzienników przesyłania strumieniowego](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Zrzut ekranu przedstawiający monit VS Code o włączenie rejestrowania plików i ponowne uruchomienie aplikacji sieci Web z wybranym przyciskiem tak.":::

1. Po kilku sekundach zobaczysz komunikat informujący o tym, że masz połączenie z usługą przesyłania strumieniowego dziennika. 
1. Odśwież stronę kilka razy, aby zobaczyć więcej działań.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Wyczyść zasoby

Znajdź aplikację w Eksploratorze **usługi Azure App Service** , kliknij prawym przyciskiem myszy nazwę aplikacji, a następnie wybierz polecenie **Usuń**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Znajdź aplikację w Eksploratorze &quot;* *&quot; AZURE APP SERVICE &quot;* *&quot;, kliknij prawym przyciskiem myszy nazwę aplikacji, a następnie wybierz polecenie &quot;Usuń&quot;.":::

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się ukończyć ten przewodnik Szybki Start. Zmiany w tej aplikacji można wdrożyć przy użyciu tego samego procesu i wybierając istniejącą aplikację zamiast tworzenia nowej.

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub uzyskać wszystkie te elementy, instalując [pakiet Node Pack dla platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .