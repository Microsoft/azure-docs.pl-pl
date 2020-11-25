---
title: Wdróż lokalnie rozwiązanie do monitorowania zdalnego — Docker-Azure | Microsoft Docs
description: Ten przewodnik przedstawia sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej przy użyciu platformy Docker na potrzeby testowania i programowania.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020255"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Wdrażanie akceleratora rozwiązania do monitorowania zdalnego lokalnie — Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule opisano sposób wdrażania akceleratora rozwiązania do monitorowania zdalnego na maszynie lokalnej na potrzeby testowania i programowania. Dowiesz się, jak wdrożyć mikrousługi w lokalnych kontenerach platformy Docker. Lokalne wdrożenie mikrousług używa następujących usług w chmurze: IoT Hub, Cosmos DB, Azure Streaming Analytics i Azure Time Series Insights Services w chmurze.

Jeśli chcesz uruchomić Akcelerator rozwiązania do monitorowania zdalnego w środowisku IDE na komputerze lokalnym, zobacz [wdrażanie akceleratora rozwiązań zdalnego monitorowania lokalnie — Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wdrożyć usługi platformy Azure używane przez Akcelerator rozwiązania do monitorowania zdalnego, wymagana jest aktywna subskrypcja platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja komputera

Aby ukończyć lokalne wdrożenie, potrzebne są następujące narzędzia zainstalowane na lokalnym komputerze deweloperskim:

* [Narzędzia](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) — Jeśli planujesz wprowadzić zmiany w mikrousługach.
* [Node.js V8](https://nodejs.org/) — to oprogramowanie jest warunkiem wstępnym dla komputerów, które są używane przez skrypty do tworzenia zasobów platformy Azure. Nie używaj Node.js v10.

> [!NOTE]
> Te narzędzia są dostępne na wielu platformach, w tym w systemach Windows, Linux i iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Uruchamianie mikrousług w Docker

Otwórz nowy wiersz polecenia, aby upewnić się, że masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **Start. cmd** . W systemie Windows można sprawdzić, czy zmienne środowiskowe są ustawiane przez uruchomienie następującego polecenia:

```cmd
set PCS
```

Polecenie pokazuje wszystkie zmienne środowiskowe ustawione przez skrypt **Start. cmd** .

Upewnij się, że platforma Docker jest uruchomiona na komputerze lokalnym.
> [!NOTE]
> Program Docker musi mieć uruchomione [kontenery systemu Linux](https://docs.docker.com/docker-for-windows/) , jeśli jest uruchomiony w systemie Windows.

Mikrousługi działające w lokalnych kontenerach platformy Docker muszą uzyskiwać dostęp do usług w chmurze platformy Azure. Możesz przetestować łączność z Internetem środowiska Docker przy użyciu następującego polecenia, aby wysłać polecenie ping do adresu internetowego z wewnątrz kontenera:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Aby uruchomić Akcelerator rozwiązania, przejdź do folderu **usługi \\ skrypty \\ lokalne** w środowisku wiersza polecenia i uruchom następujące polecenie:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Przed uruchomieniem programu upewnij się, że [dysk lokalny został udostępniony](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) przy użyciu platformy Docker `docker-compose up` .

Przy pierwszym uruchomieniu tego polecenia usługa Docker pobiera obrazy mikrousług z usługi Docker Hub w celu lokalnego tworzenia kontenerów. W przypadku następujących uruchomień Aparat Docker natychmiast uruchamia kontenery.

> [!TIP]
> Firma Microsoft często publikuje nowe obrazy platformy Docker przy użyciu nowych funkcji. Możesz użyć następującego zestawu poleceń do oczyszczenia lokalnych kontenerów platformy Docker i odpowiednich obrazów przed pobraniem najnowszych danych:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Aby wyświetlić dzienniki z kontenera, można użyć oddzielnej powłoki. Najpierw Znajdź identyfikator kontenera za pomocą `docker ps` polecenia. Następnie użyj, `docker logs {container-id} --tail 1000` Aby wyświetlić ostatnie 1000 wpisów dla określonego kontenera.

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie Stream Analytics:

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** utworzonej dla Twojego rozwiązania. Nazwa grupy zasobów jest nazwą wybraną dla rozwiązania po uruchomieniu skryptu **Start. cmd** .
1. Kliknij **zadanie Stream Analytics** na liście zasobów.
1. Na stronie **Przegląd** zadania Stream Analytics kliknij przycisk **Uruchom** . Następnie kliknij przycisk **Uruchom** , aby uruchomić zadanie teraz.

### <a name="connect-to-the-dashboard"></a>Nawiązywanie połączenia z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania do monitorowania zdalnego, przejdź do `http://localhost:8080` przeglądarki. Teraz można korzystać z interfejsu użytkownika sieci Web i lokalnych mikrousług.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania Usuń usługi w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [Azure Portal](https://ms.portal.azure.com) i Usuń grupę zasobów utworzoną przez skrypt **Start. cmd** .

Użyj `docker-compose down --rmi all` polecenia, aby usunąć obrazy platformy Docker i zwolnić miejsce na komputerze lokalnym. Możesz również usunąć kopię lokalną repozytorium zdalnego monitorowania utworzonego podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do monitorowania zdalnego, następnym krokiem jest zapoznanie się z [możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).
