---
title: Samouczek służący do filtrowania, analizowania danych przy użyciu obliczeń na Azure Stack Edge — procesor GPU Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową w Azure Stack Edge GPU i użyć jej do przekształcenia danych przed wysłaniem do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c884ad6850b8f94baa7c658d685651c3241be33f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935690"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Samouczek: Konfigurowanie obliczeń na urządzeniach z procesorem GPU w Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym samouczku opisano, jak skonfigurować rolę obliczeniową i utworzyć klaster Kubernetes na urządzeniu Azure Stack EDGE Pro. 

Wykonanie tej procedury może potrwać od 20 do 30 minut.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Pobierz punkty końcowe Kubernetes

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem roli obliczeniowej na urządzeniu z systemem Azure Stack EDGE Pro upewnij się, że:

- Twoje urządzenie Azure Stack EDGE Pro zostało aktywowane zgodnie z opisem w artykule [aktywuj Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).
- Upewnij się, że wykonano instrukcje zawarte w temacie [Włączanie sieci obliczeniowej](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) i:
    - Włączono interfejs sieciowy dla obliczeń.
    - Przypisani adresy IP i Kubernetes usług zewnętrznych usługi Kubernetes.

## <a name="configure-compute"></a>Konfigurowanie obliczeń

W celu skonfigurowania obliczeń na Azure Stack EDGE Pro utworzysz zasób IoT Hub za pośrednictwem Azure Portal.

1. W Azure Portal Azure Stack zasobów brzegowych przejdź do **omówienia** i wybierz pozycję **IoT Edge**.

   ![Wprowadzenie do obliczeń](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-1.png)

2. W obszarze **Włączanie usługi IoT Edge** wybierz pozycję **Dodaj**.

   ![Konfigurowanie obliczeń](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-2.png)

3. W bloku **Konfigurowanie obliczeń krawędzi** wprowadź następujące informacje:
   
   |Pole  |Wartość  |
   |---------|---------|
   |Usługa IoT Hub     | Wybierz pozycję **Nowy** lub **istniejący**. <br> Domyślnie warstwa standardowa (S1) jest używana do tworzenia zasobu IoT. Aby użyć zasobu IoT warstwy Bezpłatna, utwórz go, a następnie wybierz istniejący zasób. <br> W każdym przypadku zasób IoT Hub używa tej samej subskrypcji i grupy zasobów, która jest używana przez zasób Azure Stack Edge.     |
   |Nazwa     |Wprowadź nazwę dla zasobu IoT Hub.         |

   ![Wprowadzenie do obliczeń obliczeniowych 2](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-3.png)

4. Po zakończeniu ustawień wybierz pozycję **Przegląd + Utwórz**. Sprawdź ustawienia zasobu IoT Hub i wybierz pozycję **Utwórz**.

   Tworzenie zasobów dla zasobu IoT Hub trwa kilka minut. Po utworzeniu zasobu **Przegląd** wskazuje, że usługa IoT Edge jest teraz uruchomiona.

   ![Wprowadzenie do obliczeń obliczeniowych 3](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-4.png)

5. Aby upewnić się, że skonfigurowano rolę obliczeń brzegowych, wybierz pozycję **Właściwości**.

   ![Wprowadzenie do obliczeń obliczeniowych 4](./media/azure-stack-edge-gpu-deploy-configure-compute/configure-compute-5.png)

   Po skonfigurowaniu roli funkcji obliczeniowej Edge na urządzeniu Edge tworzone są dwa urządzenia: urządzenie IoT i urządzenie IoT Edge. Oba urządzenia można wyświetlić w zasobie usługi IoT Hub. Środowisko uruchomieniowe IoT Edge jest również uruchomione na tym urządzeniu IoT Edge. W chwili obecnej dla urządzenia IoT Edge jest dostępna tylko platforma Linux.

Skonfigurowanie obliczeń może potrwać 20-30 minut, ponieważ w tle są tworzone maszyny wirtualne i klaster Kubernetes.

Po pomyślnym skonfigurowaniu obliczeń w Azure Portal jest klaster Kubernetes i domyślny użytkownik skojarzony z przestrzenią nazw IoT (systemowa przestrzeń nazw kontrolowana przez Azure Stack EDGE Pro) istnieje.

## <a name="get-kubernetes-endpoints"></a>Pobierz punkty końcowe Kubernetes

Aby skonfigurować klienta do uzyskiwania dostępu do klastra Kubernetes, wymagany jest punkt końcowy Kubernetes. Wykonaj następujące kroki, aby uzyskać punkt końcowy interfejsu API Kubernetes z lokalnego interfejsu użytkownika urządzenia z Azure Stack EDGE Pro.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do strony **urządzenia** .
2. W obszarze **punkty końcowe urządzenia** Skopiuj punkt końcowy **usługi interfejsu API Kubernetes** . Ten punkt końcowy jest ciągiem w następującym formacie: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Strona urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-j-series-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Zapisz ciąg punktu końcowego. Ten ciąg punktu końcowego będzie używany później podczas konfigurowania klienta do uzyskiwania dostępu do klastra Kubernetes za pośrednictwem polecenia kubectl.

4. Gdy jesteś w lokalnym interfejsie użytkownika sieci Web, możesz:

    - Przejdź do interfejsu API Kubernetes, wybierz pozycję **Ustawienia zaawansowane** i Pobierz zaawansowany plik konfiguracji dla Kubernetes. 

        ![Strona urządzenia w lokalnym interfejsie użytkownika 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Jeśli podano klucz od firmy Microsoft (wybierz opcję Użytkownicy mogą mieć klucz), możesz użyć tego pliku konfiguracji.

        ![Strona urządzenia w lokalnym interfejsie użytkownika 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Możesz również przejść do punktu końcowego **pulpitu nawigacyjnego Kubernetes** i pobrać `aseuser` plik konfiguracji. 
    
        ![Strona urządzenia w lokalnym interfejsie użytkownika 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Ten plik konfiguracji służy do logowania się do pulpitu nawigacyjnego Kubernetes lub debugowania wszelkich problemów w klastrze Kubernetes. Aby uzyskać więcej informacji, zobacz [dostęp do pulpitu nawigacyjnego usługi Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie obliczeń
> * Pobierz punkty końcowe Kubernetes


Aby dowiedzieć się, jak administrować urządzeniem Azure Stack EDGE Pro, zobacz:

> [!div class="nextstepaction"]
> [Korzystanie z lokalnego interfejsu użytkownika sieci Web do administrowania Azure Stack krawędzią Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
