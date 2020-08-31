---
title: Samouczek służący do filtrowania, analizowania danych przy użyciu obliczeń na Azure Stack Edge | Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową na Azure Stack Edge i użyć jej do przekształcenia danych przed wysłaniem do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 9fd940ec5cfb3eac9d0072c8554ca6bd295a50ec
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89088086"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-gpu-device"></a>Samouczek: Konfigurowanie obliczeń na Azure Stack urządzeniach procesora GPU Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym samouczku opisano, jak skonfigurować rolę obliczeniową i utworzyć klaster Kubernetes na urządzeniu brzegowym Azure Stack. 

Wykonanie tej procedury może potrwać od 20 do 30 minut.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Pobierz punkt końcowy interfejsu API Kubernetes

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu z systemem Azure Stack Edge upewnij się, że:

- Aktywowano urządzenie Azure Stack Edge zgodnie z opisem w artykule [aktywuj Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md).
- Upewnij się, że wykonano instrukcje zawarte w temacie [Włączanie sieci obliczeniowej](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) i:
    - Włączono interfejs sieciowy dla obliczeń.
    - Przypisani adresy IP i Kubernetes usług zewnętrznych usługi Kubernetes.

## <a name="configure-compute"></a>Konfigurowanie obliczeń

Aby skonfigurować obliczenia na Azure Stackej krawędzi, utworzysz zasób IoT Hub za pośrednictwem Azure Portal.

1. W Azure Portal Azure Stack zasobów brzegowych przejdź do **omówienia**. W okienku po prawej stronie na kafelku **Oblicz** wybierz pozycję **Rozpocznij**.

    ![Wprowadzenie do obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Na kafelku **Konfigurowanie obliczeń krawędzi** wybierz pozycję **Konfiguruj obliczenia**.

    ![Konfigurowanie obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. W bloku **Konfigurowanie obliczeń krawędzi** wprowadź następujące dane:

   
    |Pole  |Wartość  |
    |---------|---------|
    |Usługa IoT Hub     | Wybierz pozycję **Nowy** lub **istniejący**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Azure Stack Edge.     |
    |Nazwa     |Wprowadź nazwę dla zasobu IoT Hub.         |

    ![Wprowadzenie do obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Wybierz pozycję **Utwórz**. Tworzenie zasobów IoT Hub trwa kilka minut. Po utworzeniu zasobu IoT Hub, **Skonfiguruj kafelek Oblicz** aktualizacje, aby pokazać konfigurację obliczeń. 

    ![Wprowadzenie do obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Aby upewnić się, że skonfigurowano rolę obliczeniową brzegową, wybierz pozycję **Wyświetl obliczenia** na kafelku **Konfiguruj obliczenia** .
    
    ![Wprowadzenie do obliczeń](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Jeśli okno dialogowe **Konfigurowanie obliczeń** zostanie zamknięte przed skojarzeniem IoT Hub z urządzeniem brzegowym Azure Stack, IoT Hub zostanie utworzone, ale nie będzie wyświetlane w konfiguracji obliczeń. 
    
Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.

Skonfigurowanie obliczeń może potrwać 20-30 minut, ponieważ w tle są tworzone maszyny wirtualne i klaster Kubernetes. 

Po pomyślnym skonfigurowaniu obliczeń w Azure Portal jest klaster Kubernetes i domyślny użytkownik skojarzony z przestrzenią nazw IoT (systemowa przestrzeń nazw kontrolowana przez Azure Stack Edge) istnieje. 

## <a name="get-kubernetes-api-endpoint"></a>Pobierz punkt końcowy interfejsu API Kubernetes

Aby skonfigurować klienta do uzyskiwania dostępu do klastra Kubernetes, wymagany jest punkt końcowy Kubernetes. Wykonaj następujące kroki, aby uzyskać punkt końcowy interfejsu API Kubernetes z lokalnego interfejsu użytkownika urządzenia brzegowego Azure Stack.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do strony **urządzenia** .
2. W obszarze **punkty końcowe urządzenia**Skopiuj punkt końcowy **usługi interfejsu API Kubernetes** . Ten punkt końcowy jest ciągiem w następującym formacie: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Strona urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Zapisz ciąg punktu końcowego. Będzie on używany później podczas konfigurowania klienta do uzyskiwania dostępu do klastra Kubernetes za pośrednictwem polecenia kubectl.

4. Gdy jesteś w lokalnym interfejsie użytkownika sieci Web, wybierz pozycję **Ustawienia zaawansowane** i Pobierz plik konfiguracji. 

    ![Strona urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-j-series-create-kubernetes-cluster/advanced-config-1.png)

    Jeśli podano klucz od firmy Microsoft (wybierz opcję Użytkownicy mogą mieć ten element), możesz użyć tego pliku konfiguracji.

    ![Strona urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-j-series-create-kubernetes-cluster/advanced-config-2.png)


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Pobierz punkt końcowy interfejsu API Kubernetes


Aby dowiedzieć się, jak administrować urządzeniem brzegowym Azure Stack, zobacz:

> [!div class="nextstepaction"]
> [Używanie lokalnego interfejsu użytkownika sieci Web do administrowania Azure Stack krawędzią](azure-stack-edge-manage-access-power-connectivity-mode.md)
