---
title: Wdrażanie rozwiązania do monitorowania zdalnego lokalnie — VS IDE — Azure | Microsoft Docs
description: Ten przewodnik przedstawia sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej przy użyciu programu Visual Studio do testowania i programowania.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73890887"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Wdrażanie akceleratora rozwiązań zdalnego monitorowania lokalnie — Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule opisano sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej na potrzeby testowania i programowania. Dowiesz się, jak uruchamiać mikrousługi w programie Visual Studio. Lokalne wdrożenie mikrousług używa następujących usług w chmurze: IoT Hub, Cosmos DB, Azure Streaming Analytics i Azure Time Series Insights Services w chmurze.

Jeśli chcesz uruchomić Akcelerator rozwiązania do zdalnego monitorowania w programie Docker na komputerze lokalnym, zobacz [wdrażanie akceleratora rozwiązania do zdalnego monitorowania](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć usługi platformy Azure używane przez Akcelerator rozwiązania do monitorowania zdalnego, wymagana jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja komputera

Aby ukończyć lokalne wdrożenie, potrzebne są następujące narzędzia zainstalowane na lokalnym komputerze deweloperskim:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Program Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js V8](https://nodejs.org/) — to oprogramowanie jest warunkiem wstępnym dla komputerów, które są używane przez skrypty do tworzenia zasobów platformy Azure. Nie używaj Node.js v10.

> [!NOTE]
> Program Visual Studio jest dostępny dla systemów Windows i Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji uruchomiono mikrousługi zdalnego monitorowania. Interfejs użytkownika sieci Web można uruchomić natywnie, usługi symulacji urządzenia w Docker i mikrousługi w programie Visual Studio.

### <a name="run-the-device-simulation-service"></a>Uruchamianie usługi symulacji urządzenia

Otwórz nowe okno wiersza polecenia, aby upewnić się, że masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **Start. cmd** w poprzedniej sekcji.

Uruchom następujące polecenie, aby uruchomić kontener platformy Docker dla usługi symulacji urządzenia. Usługa symuluje urządzenia dla rozwiązania do zdalnego monitorowania.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdróż wszystkie mikrousługi na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousługi zdalnego monitorowania w programie Visual Studio:

1. Uruchom program Visual Studio.
1. Otwórz rozwiązanie **Remote-Monitoring. sln** w folderze **Services** w lokalnej kopii repozytorium.
1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie, a następnie kliknij pozycję **Właściwości**.
1. Wybierz pozycję **wspólne właściwości, > projekt startowy**.
1. Wybierz **wiele projektów startowych** i ustaw **akcję** do **uruchomienia** dla następujących projektów:
    * Usługa sieci Web (asa-manager\WebService)
    * Usługa sieci Web (auth\WebService)
    * Usługa sieci Web (config\WebService)
    * Usługa sieci Web (device-telemetry\WebService)
    * Usługa sieci Web (iothub-manager\WebService)
    * Usługa sieci Web (storage-adapter\WebService)
1. Kliknij przycisk **OK** , aby zapisać wybrane opcje.
1. Kliknij pozycję **debuguj > Rozpocznij debugowanie** , aby skompilować i uruchomić usługi sieci Web na komputerze lokalnym.

Każda usługa sieci Web otwiera wiersz polecenia i okno przeglądarki sieci Web. W wierszu polecenia są wyświetlane dane wyjściowe z uruchomionej usługi, a okno przeglądarki umożliwia monitorowanie stanu. Nie zamykaj wierszy poleceń ani stron sieci Web, ta akcja powoduje zatrzymanie usługi sieci Web.

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie Stream Analytics:

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** utworzonej dla Twojego rozwiązania. Nazwa grupy zasobów jest nazwą wybraną dla rozwiązania po uruchomieniu skryptu **Start. cmd** .
1. Kliknij **zadanie Stream Analytics** na liście zasobów.
1. Na stronie **Przegląd** zadania Stream Analytics kliknij przycisk **Uruchom** . Następnie kliknij przycisk **Uruchom** , aby uruchomić zadanie teraz.

### <a name="run-the-web-ui"></a>Uruchamianie interfejsu użytkownika sieci Web

W tym kroku zostanie uruchomiony interfejs użytkownika sieci Web. Otwórz nowe okno wiersza polecenia, aby upewnić się, że masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **Start. cmd** . Przejdź do folderu **WebUI** w lokalnej kopii repozytorium i uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu, przeglądarka wyświetla stronę **http: \/ /localhost: 3000/pulpit nawigacyjny**. Błędy na tej stronie są oczekiwane. Aby wyświetlić aplikację bez błędów, wykonaj następujące czynności.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie NGINX

Skonfiguruj odwrotny serwer proxy, aby połączyć aplikację sieci Web i mikrousługi uruchomione na komputerze lokalnym:

* Skopiuj plik **Nginx. conf** z folderu **webui\scripts\localhost** w lokalnej kopii repozytorium do katalogu instalacyjnego **nginx\conf** .
* Uruchom **Nginx**.

Aby uzyskać więcej informacji na temat uruchamiania programu **Nginx**, zobacz [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Nawiązywanie połączenia z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania do monitorowania zdalnego, przejdź do protokołu http: \/ /localhost: 9000 w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania Usuń usługi w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [Azure Portal](https://ms.portal.azure.com) i Usuń grupę zasobów utworzoną przez skrypt **Start. cmd** .

Możesz również usunąć kopię lokalną repozytorium zdalnego monitorowania utworzonego podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do monitorowania zdalnego, następnym krokiem jest zapoznanie się z [możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).
