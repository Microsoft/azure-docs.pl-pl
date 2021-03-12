---
title: Samouczek służący do filtrowania, analizowania danych przy użyciu obliczeń na Azure Stack Edge — procesor GPU Microsoft Docs
description: Dowiedz się, jak skonfigurować rolę obliczeniową w Azure Stack Edge GPU i użyć jej do przekształcenia danych przed wysłaniem do platformy Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633544"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Samouczek: Konfigurowanie obliczeń na urządzeniach z procesorem GPU w Azure Stack Edge

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym samouczku opisano, jak skonfigurować rolę obliczeniową i utworzyć klaster Kubernetes na urządzeniu Azure Stack EDGE Pro. 

Wykonanie tej procedury może potrwać od 20 do 30 minut.


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

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

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Pobierz punkty końcowe Kubernetes

Aby skonfigurować klienta do uzyskiwania dostępu do klastra Kubernetes, wymagany jest punkt końcowy Kubernetes. Wykonaj następujące kroki, aby uzyskać punkt końcowy interfejsu API Kubernetes z lokalnego interfejsu użytkownika urządzenia z Azure Stack EDGE Pro.

1. W lokalnym interfejsie użytkownika sieci Web urządzenia przejdź do strony **urządzenia** .
2. W obszarze **punkty końcowe urządzenia** Skopiuj punkt końcowy **usługi interfejsu API Kubernetes** . Ten punkt końcowy jest ciągiem w następującym formacie: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Strona urządzenia w lokalnym interfejsie użytkownika](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

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
