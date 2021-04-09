---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99524924"
---
W celu skonfigurowania obliczeń na Azure Stack EDGE Pro utworzysz zasób IoT Hub za pośrednictwem Azure Portal.

1. W Azure Portal Azure Stack zasobów brzegowych przejdź do **omówienia** i wybierz pozycję **IoT Edge**.

   ![Wprowadzenie do obliczeń](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. W obszarze **Włączanie usługi IoT Edge** wybierz pozycję **Dodaj**.

   ![Konfigurowanie obliczeń](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. W bloku **Konfigurowanie obliczeń krawędzi** wprowadź następujące informacje:
   
    |Pole  |Wartość  |
    |---------|---------|
    |Subskrypcja     |Wybierz subskrypcję dla zasobu IoT Hub. Możesz użyć tej samej subskrypcji, która jest używana przez zasób Azure Stack Edge.         |
    |Grupa zasobów     |Wybierz grupę zasobów dla zasobu IoT Hub. Możesz użyć tej samej grupy zasobów, która jest używana przez zasób Azure Stack Edge.         |
    |Usługa IoT Hub     | Wybierz pozycję **Nowy** lub **istniejący**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób usługi IoT Hub używa tej samej subskrypcji i grupy zasobów, które są używane przez zasób usługi Azure Stack Edge.     |
    |Nazwa     |Zaakceptuj nazwę domyślną lub wprowadź nazwę dla zasobu IoT Hub.         |

   ![Wprowadzenie do obliczeń obliczeniowych 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Po zakończeniu ustawień wybierz pozycję **Przegląd + Utwórz**. Sprawdź ustawienia zasobu IoT Hub i wybierz pozycję **Utwórz**.

   Tworzenie zasobów dla zasobu IoT Hub trwa kilka minut. Po utworzeniu zasobu **Przegląd** wskazuje, że usługa IoT Edge jest teraz uruchomiona.

   ![Wprowadzenie do obliczeń obliczeniowych 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Aby potwierdzić, że skonfigurowano rolę obliczeń brzegowych, przejdź do pozycji **IoT Edge > właściwości**.

   ![Wprowadzenie do obliczeń obliczeniowych 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.

Skonfigurowanie obliczeń może potrwać 20-30 minut, ponieważ w tle są tworzone maszyny wirtualne i klaster Kubernetes.

Po pomyślnym skonfigurowaniu obliczeń w Azure Portal jest klaster Kubernetes i domyślny użytkownik skojarzony z przestrzenią nazw IoT (systemowa przestrzeń nazw kontrolowana przez Azure Stack EDGE Pro) istnieje.
