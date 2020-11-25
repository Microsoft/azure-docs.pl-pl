---
title: Pakiet narzędzia do importowania rozwiązań do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule opisano sposób importowania pakietu IoT Edge do akceleratora rozwiązania do zdalnego monitorowania
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012292"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importowanie pakietu usługi IoT Edge do akceleratora rozwiązania do monitorowania zdalnego

Manifest wdrożenia definiuje moduły do wdrożenia na urządzeniu IoT Edge. W tym artykule założono, że deweloper w organizacji utworzył już manifest wdrożenia. Aby dowiedzieć się, jak projektant tworzy manifest, zobacz jeden z następujących IoT Edge artykuły z artykułami:

- [Wdróż moduły Azure IoT Edge z Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Wdrażanie modułów Azure IoT Edge za pomocą interfejsu wiersza polecenia platformy Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Wdróż moduły Azure IoT Edge z Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Deweloper tworzy i testuje manifest wdrożenia w środowisku programistycznym. Gdy wszystko będzie gotowe, możesz zaimportować manifest do akceleratora rozwiązania do monitorowania zdalnego.

## <a name="export-a-manifest"></a>Eksportowanie manifestu

Użyj Azure Portal, aby wyeksportować manifest wdrożenia ze środowiska programistycznego:

1. W Azure Portal przejdź do centrum IoT Hub, którego używasz do tworzenia i testowania urządzeń IoT Edge. Kliknij **IoT Edge** a następnie **IoT Edge wdrożenia**: ![ IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Kliknij wdrożenie z konfiguracją wdrożenia, której chcesz użyć. Zostanie wyświetlona strona **Szczegóły wdrożenia** : ![ IoT Edge Szczegóły wdrożenia](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Kliknij pozycję **pobierz IoT Edge manifest**:  ![ Pobierz manifest wdrożenia](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Zapisz plik JSON jako plik lokalny o nazwie **deploymentmanifest.json**.

Teraz masz plik, który zawiera manifest wdrożenia. W następnej sekcji zaimportowano ten manifest jako pakiet do rozwiązania do zdalnego monitorowania.

## <a name="import-a-package"></a>Importowanie pakietu

Wykonaj poniższe kroki, aby zaimportować manifest wdrożenia programu Edge jako pakiet do rozwiązania:

1. Przejdź do strony **pakiety** w interfejsie użytkownika sieci Web do monitorowania zdalnego:  ![ pakiety](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Kliknij pozycję **+ nowy pakiet**, wybierz opcję **manifest graniczny** jako typ pakietu, a następnie kliknij przycisk **Przeglądaj** , aby wybrać **deploymentmanifest.jsw** pliku zapisanym w poprzedniej sekcji:  ![ SELECT manifest](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Kliknij przycisk **Przekaż** , aby dodać pakiet do rozwiązania do monitorowania zdalnego:  ![ przekazany pakiet](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Plik manifestu wdrażania IoT Edge został przekazany jako pakiet. Na stronie **wdrożenia** można wdrożyć ten pakiet na podłączonych urządzeniach IoT Edge.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrażać moduły na urządzeniu IoT Edge z rozwiązania do zdalnego monitorowania, następnym krokiem jest poznanie [IoT Edge](../iot-edge/about-iot-edge.md).
