---
title: Wdrażanie modułów z poziomu Visual Studio Code — Azure IoT Edge
description: Użyj Visual Studio Code z narzędziami Azure IoT, aby wypchnąć moduł IoT Edge z IoT Hub do urządzenia IoT Edge, zgodnie z konfiguracją manifestu wdrożenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a6415aa55a67b37d9564398eb77dacb48cf16f0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378115"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Wdróż moduły Azure IoT Edge z Visual Studio Code

Po utworzeniu modułów IoT Edge za pomocą logiki biznesowej należy wdrożyć je na urządzeniach, aby działały na brzegu. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, możesz wdrożyć je wszystkie jednocześnie i zadeklarować reguły routingu, które je łączą.

W tym artykule przedstawiono sposób tworzenia manifestu wdrożenia JSON, a następnie użycia tego pliku do wypchnięcia wdrożenia na urządzenie IoT Edge. Aby uzyskać informacje na temat tworzenia wdrożenia, które jest przeznaczone dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [wdrażanie modułów IoT Edge na dużą skalę przy użyciu Visual Studio Code](how-to-deploy-vscode-at-scale.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.
* Urządzenie IoT Edge

  Jeśli nie masz skonfigurowanego urządzenia IoT Edge, możesz je utworzyć na maszynie wirtualnej platformy Azure. Wykonaj kroki opisane w jednym z artykułów z przewodnikiem Szybki Start, aby [utworzyć wirtualne urządzenie](quickstart-linux.md) z systemem Linux lub [utworzyć wirtualne urządzenie systemu Windows](quickstart.md).

* [Program Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) dla Visual Studio Code.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat działania manifestów wdrożenia i sposobu ich tworzenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).

Aby wdrożyć moduły przy użyciu Visual Studio Code, Zapisz manifest wdrożenia lokalnie jako. Plik JSON. Ścieżka do pliku zostanie użyta w następnej sekcji po uruchomieniu polecenia, aby zastosować konfigurację na urządzeniu.

Oto podstawowy manifest wdrożenia z jednym modułem:

>[!NOTE]
>Ten przykładowy manifest wdrażania używa wersji schematu 1,1 dla agenta IoT Edge i centrum. Wersja schematu 1,1 została wydana wraz z IoT Edge wersja 1.0.10 i włącza funkcje takie jak kolejność uruchamiania modułu i priorytetyzacja tras.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

Za pomocą rozszerzeń usługi Azure IoT Visual Studio Code można wykonywać operacje w usłudze IoT Hub. Aby te operacje działały, należy zalogować się do konta platformy Azure i wybrać Centrum IoT, nad którym pracujesz.

1. W Visual Studio Code Otwórz widok **Eksploratora** .

1. W dolnej części Eksploratora rozwiń sekcję **IoT Hub platformy Azure** .

   ![Rozwiń sekcję IoT Hub platformy Azure](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. Kliknij pozycję **...** w nagłówku sekcji **IoT Hub platformy Azure** . Jeśli nie widzisz wielokropka, umieść kursor nad nagłówkiem.

1. Wybierz **pozycję wybierz IoT Hub**.

1. Jeśli nie zalogowano się na koncie platformy Azure, postępuj zgodnie z monitami, aby to zrobić.

1. Wybierz swoją subskrypcję platformy Azure.

1. Wybierz Centrum IoT Hub.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

Wdrożenie modułów na urządzeniu przez zastosowanie manifestu wdrożenia, który został skonfigurowany za pomocą informacji o module.

1. W widoku Eksploratora Visual Studio Code rozwiń sekcję **IoT Hub platformy Azure** , a następnie rozwiń węzeł **urządzenia** .

1. Kliknij prawym przyciskiem myszy urządzenie IoT Edge, które chcesz skonfigurować przy użyciu manifestu wdrażania.

    > [!TIP]
    > Aby upewnić się, że wybrane urządzenie jest urządzeniem IoT Edge, wybierz je, aby rozwinąć listę modułów i sprawdzić obecność **$edgeHub** i **$edgeAgent**. Każde urządzenie IoT Edge obejmuje te dwa moduły.

1. Wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

1. Przejdź do pliku JSON manifestu wdrożenia, którego chcesz użyć, a następnie kliknij pozycję **Wybierz manifest wdrożenia Edge**.

   ![Wybieranie manifestu wdrożenia Edge](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

Wyniki wdrożenia są drukowane w VS Code danych wyjściowych. Pomyślne wdrożenia są stosowane w ciągu kilku minut, jeśli urządzenie docelowe jest uruchomione i połączone z Internetem.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich w sekcji **IoT Hub platformy Azure** . Wybierz strzałkę obok urządzenia IoT Edge, aby je rozwinąć. Zostaną wyświetlone wszystkie aktualnie uruchomione moduły.

Jeśli ostatnio wdrożono nowe moduły na urządzeniu, umieść kursor nad nagłówkiem sekcji **usługi Azure IoT Hub Devices** i wybierz ikonę Odśwież, aby zaktualizować widok.

Kliknij prawym przyciskiem myszy nazwę modułu, aby wyświetlić i edytować sznurek modułu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Visual Studio Code](how-to-deploy-at-scale.md)
