---
title: Wdróż lokalnie rozwiązanie do monitorowania zdalnego — Visual Studio Code — Azure | Microsoft Docs
description: Ten przewodnik przedstawia sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej przy użyciu Visual Studio Code do testowania i programowania.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024184"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Wdróż lokalnie Akcelerator rozwiązania do monitorowania zdalnego Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule opisano sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej na potrzeby testowania i programowania. Dowiesz się, jak uruchamiać mikrousługi w Visual Studio Code. Lokalne wdrożenie mikrousług używa następujących usług w chmurze: IoT Hub, Cosmos DB, Azure Streaming Analytics i Azure Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć usługi platformy Azure używane przez Akcelerator rozwiązania do monitorowania zdalnego, wymagana jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja komputera

Aby ukończyć lokalne wdrożenie, potrzebne są następujące narzędzia zainstalowane na lokalnym komputerze deweloperskim:

* [Narzędzia](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Rozszerzenie C# VS Code](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js V8](https://nodejs.org/) — to oprogramowanie jest warunkiem wstępnym dla komputerów, które są używane przez skrypty do tworzenia zasobów platformy Azure. Nie używaj Node.js v10

> [!NOTE]
> Visual Studio Code jest dostępny dla systemów Windows, Mac i Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji uruchomiono mikrousługi zdalnego monitorowania. Uruchamiasz interfejs użytkownika sieci Web w sposób natywny, usługę symulacji urządzenia w Docker i mikrousługi w Visual Studio Code.

### <a name="build-the-code"></a>Kompilowanie kod

Przejdź do Azure-IoT-PCs-Remote-Monitoring-dotnet\services w wierszu polecenia i uruchom następujące polecenia, aby skompilować kod.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdróż wszystkie mikrousługi na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousługi zdalnego monitorowania w Visual Studio Code:

1. Uruchom program Visual Studio Code.
1. W VS Code Otwórz folder **Azure-IoT-komputery-Remote-Monitoring-dotnet** .
1. Utwórz nowy folder o nazwie **. programu vscode** w folderze **Azure-IoT-komputery-Remote-Monitoring-dotnet** .
1. Skopiuj pliki **launch.jswłączone** i **tasks.js** z services\scripts\local\launch\idesettings\vscode do folderu **. programu vscode** , który właśnie został utworzony.
1. Otwórz **panel Debugowanie** w vs Code i uruchom polecenie **Uruchom wszystkie mikrousługi** . Ta konfiguracja uruchamia mikrousługę symulacji urządzenia w Docker i uruchamia inne mikrousługi w debugerze.

Wyjście z uruchamiania **Uruchom wszystkie microsoervices** w konsoli debugowania wygląda następująco:

[![Wdrażanie-lokalne-mikrousługi](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Uruchamianie interfejsu użytkownika sieci Web

W tym kroku zostanie uruchomiony interfejs użytkownika sieci Web. Przejdź do folderu **Azure-IoT-PCs-Remote-Monitoring-dotnet\webui** w lokalnej kopii i uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu, przeglądarka wyświetla stronę **http: \/ /localhost: 3000/pulpit nawigacyjny**. Błędy na tej stronie są oczekiwane. Aby wyświetlić aplikację bez błędów, wykonaj następujące czynności.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie NGINX

Skonfiguruj odwrotny serwer proxy, aby połączyć aplikację sieci Web i mikrousługi uruchomione na komputerze lokalnym:

* Skopiuj plik **Nginx. conf** z folderu **webui\scripts\localhost** do katalogu instalacyjnego **nginx\conf** .
* Uruchom **Nginx**.

Aby uzyskać więcej informacji na temat uruchamiania programu **Nginx**, zobacz [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Nawiązywanie połączenia z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania do monitorowania zdalnego, przejdź do protokołu http: \/ /localhost: 9000 w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania Usuń usługi w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [Azure Portal](https://ms.portal.azure.com) i Usuń grupę zasobów utworzoną przez skrypt **Start. cmd** .

Możesz również usunąć kopię lokalną repozytorium zdalnego monitorowania utworzonego podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do monitorowania zdalnego, następnym krokiem jest zapoznanie się z [możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).
