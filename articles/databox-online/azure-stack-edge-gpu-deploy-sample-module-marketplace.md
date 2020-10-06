---
title: Wdrażanie modułu GPU na urządzeniu z systemem Microsoft Azure Stack Edge w witrynie Azure Marketplace | Microsoft Docs
description: Opisuje sposób wdrażania modułu IoT z włączonym procesorem GPU na urządzeniu z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 64d028892298a70e7588863bf9a3f4fc6f4ca609
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760063"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Wdrażanie modułu IoT z włączonym procesorem GPU z poziomu portalu Azure Marketplace na urządzeniu Azure Stack EDGE Pro GPU

W tym artykule opisano sposób wdrażania IoT Edge modułu procesora graficznego (GPU) w witrynie Azure Marketplace na urządzeniu Azure Stack EDGE Pro. 

W tym artykule omówiono sposób wykonywania następujących zadań:
  - Przygotuj Azure Stack EDGE Pro, aby uruchomić moduł GPU.
  - Pobierz i Wdróż moduł IoT z włączonym procesorem GPU z poziomu portalu Azure Marketplace.
  - Monitoruj dane wyjściowe modułu.

## <a name="about-sample-module"></a>Informacje o przykładowym module

Moduł przykładowego procesora GPU w tym artykule zawiera przykładowy kod testu porównawczego PyTorch i TensorFlow dla procesora CPU w porównaniu z procesorem GPU.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Masz dostęp do procesora GPU z włączonym 1 węzłem Azure Stack urządzeniem brzegowym. To urządzenie jest aktywowane przy użyciu zasobu na platformie Azure. 
- Na tym urządzeniu skonfigurowano obliczenia. Wykonaj kroki opisane w [samouczku: Konfigurowanie obliczeń na urządzeniu Azure Stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md).
- Następujące zasoby programistyczne na kliencie systemu Windows:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)   


## <a name="get-module-from-azure-marketplace"></a>Pobierz moduł z witryny Azure Marketplace

1. Przeglądaj wszystkie [aplikacje w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Przeglądanie aplikacji w portalu Azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Wyszukaj **wprowadzenie do procesorów GPU**.

    ![Wyszukiwanie przykładowego modułu GPU](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Wybierz pozycję **Pobierz teraz**.

    ![Pobieranie przykładowego modułu](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Wybierz pozycję **Kontynuuj** , aby potwierdzić warunki użytkowania dostawcy i zasady zachowania poufności informacji. 

    ![Pobierz przykładowy moduł 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Wybierz subskrypcję, która została użyta do wdrożenia urządzenia z usługą Azure Stack EDGE Pro.

    ![Wybieranie subskrypcji](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Wprowadź nazwę usługi IoT Hub, która została utworzona podczas konfigurowania urządzenia z systemem Azure Stack EDGE Pro. Aby znaleźć tę nazwę usługi IoT Hub, przejdź do Azure Stack zasobu brzegowego skojarzonego z urządzeniem w Azure Portal. 

    1. W okienku po lewej stronie Opcje menu Przejdź do pozycji **Oblicz obliczenia > wprowadzenie**. 

    1. Na kafelku **Konfigurowanie obliczeń brzegowych** wybierz pozycję **Wyświetl konfigurację**. 

        ![Wyświetl konfigurację obliczeń](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. W bloku **Konfiguracja obliczeń krawędzi** :

        1. Zanotuj usługę IoT Hub, która została utworzona podczas konfigurowania obliczeń na urządzeniu z systemem Azure Stack EDGE Pro.
        2. Zanotuj nazwę urządzenia IoT Edge, które zostało utworzone podczas konfigurowania obliczeń. Ta nazwa będzie używana w kolejnym kroku.

        ![Konfiguracja obliczeń brzegowych](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Wybierz pozycję **Wdróż na urządzeniu**.

11. Wprowadź nazwę urządzenia IoT Edge lub wybierz pozycję **Znajdź urządzenie**,   Aby przeglądać urządzenia zarejestrowane w centrum.

    ![Znajdź urządzenie](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Wybierz pozycję **Utwórz**,   Aby kontynuować standardowy proces konfigurowania manifestu wdrożenia, w tym w razie potrzeby Dodaj inne moduły. Szczegóły dotyczące nowego modułu, takie jak identyfikator URI obrazu, opcje tworzenia i żądane właściwości, są wstępnie zdefiniowane, ale można je zmienić.

    ![Wybieranie pozycji Utwórz](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Sprawdź, czy moduł został wdrożony w IoT Hub w Azure Portal. Wybierz urządzenie, wybierz pozycję **Ustaw moduły**,   a moduł powinien zostać wyświetlony w sekcji **IoT Edge moduły**   .

    ![Wybierz pozycję Utwórz 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Monitorowanie modułu  

1. W palecie poleceń programu VS Code wybierz i uruchom polecenie **Azure IoT Hub: wybierz centrum IoT Hub**.

2. Wybierz subskrypcję i centrum IoT Hub zawierające urządzenie usługi IoT Edge, które chcesz skonfigurować. W takim przypadku wybierz subskrypcję używaną do wdrożenia urządzenia z usługą Azure Stack Edge, a następnie wybierz urządzenie IoT Edge utworzone dla urządzenia z Azure Stack Edge. Dzieje się tak w przypadku konfigurowania obliczeń za pośrednictwem Azure Portal w poprzednich krokach.

3. W Eksploratorze VS Code rozwiń sekcję IoT Hub platformy Azure. W obszarze **urządzenia**powinna zostać wyświetlona IoT Edge urządzenie odpowiadające urządzeniu Azure Stack EDGE Pro. 

    1. Wybierz to urządzenie, kliknij prawym przyciskiem myszy i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.
  
        ![Rozpocznij monitorowanie](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Przejdź do pozycji **urządzenia > moduły** i sprawdź, czy **moduł GPU** jest uruchomiony.

    3. Terminal VS Code powinien także pokazać zdarzenia IoT Hub jako dane wyjściowe monitorowania dla urządzenia z Azure Stack Edge.

        ![Dane wyjściowe monitorowania](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Można zobaczyć, że czas potrzebny do wykonania tego samego zestawu operacji (5000 iteracji kształtu) przez procesor GPU jest mniejszy niż w przypadku procesora CPU.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o sposobie [konfigurowania procesora GPU do korzystania z modułu](azure-stack-edge-j-series-configure-gpu-modules.md).
