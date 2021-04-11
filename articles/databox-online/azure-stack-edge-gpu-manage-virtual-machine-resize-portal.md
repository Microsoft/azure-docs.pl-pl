---
title: Zmień rozmiar maszyn wirtualnych na Azure Stack Edge — procesor GPU, Pro R, mini R za pośrednictwem Azure Portal
description: Dowiedz się, jak zmienić rozmiar maszyn wirtualnych uruchomionych na komputerze, na którym znajduje się Azure Stack brzegowej procesora GPU, Azure Stack EDGE Pro R, Azure Stack Edge mini R za pośrednictwem Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080516"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Użyj Azure Portal, aby zmienić rozmiar maszyn wirtualnych na Azure Stack

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule wyjaśniono, jak zmienić rozmiar maszyn wirtualnych wdrożonych na urządzeniu z systemem Azure Stack Edge na procesor GPU.

       
## <a name="about-vm-sizing"></a>Informacje o wymiarze maszyny wirtualnej

Rozmiar maszyny wirtualnej określa ilość zasobów obliczeniowych (takich jak procesor CPU, procesor GPU i pamięć), które są dostępne dla maszyny wirtualnej. Maszyny wirtualne należy tworzyć przy użyciu odpowiedniego rozmiaru maszyny wirtualnej dla obciążenia aplikacji. 

Mimo że wszystkie maszyny będą uruchomione na tym samym sprzęcie, rozmiary maszyn mają różne limity dostępu do dysku. Może to ułatwić zarządzanie ogólnym dostępem do dysku na maszynach wirtualnych. W przypadku zwiększenia obciążenia można również zmienić rozmiar istniejącej maszyny wirtualnej.

Aby uzyskać więcej informacji, zobacz [obsługiwane rozmiary maszyn wirtualnych na urządzeniu](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Wymagania wstępne

Przed zmianą rozmiaru maszyny wirtualnej działającej na urządzeniu za pomocą Azure Portal upewnij się, że:

1. Na urządzeniu została wdrożona co najmniej jedna maszyna wirtualna. Aby utworzyć tę maszynę wirtualną, zapoznaj się z instrukcjami w temacie [Wdrażanie maszyny wirtualnej na Azure Stack brzegowej Pro za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Maszyna wirtualna powinna być w stanie **zatrzymania** . Aby zatrzymać MASZYNę wirtualną, przejdź do pozycji **maszyny wirtualne > przegląd** i wybierz maszynę wirtualną, która ma zostać zatrzymana. Na stronie Przegląd wybierz pozycję **Zatrzymaj** , a następnie wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. Przed zmianą rozmiaru maszyny wirtualnej należy zatrzymać maszynę wirtualną.

    ![Zatrzymaj maszynę wirtualną na stronie Przegląd](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej

Wykonaj następujące kroki, aby zmienić rozmiar maszyny wirtualnej wdrożonej na urządzeniu. 

1. Przejdź do zatrzymanej maszyny wirtualnej, a następnie przejdź do strony **Przegląd** . Wybierz **rozmiar maszyny wirtualnej (Zmień)**.
    
    ![Wybieranie zmiany rozmiaru maszyny wirtualnej na stronie przeglądu](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. W bloku **zmiana rozmiaru maszyny wirtualnej** na pasku poleceń wybierz **rozmiar maszyny wirtualnej** , a następnie wybierz pozycję **Zmień**.

    ![Wybierz nowy rozmiar maszyny wirtualnej](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Zobaczysz powiadomienie, że maszyna wirtualna jest aktualizowana. Po pomyślnym zaktualizowaniu maszyny wirtualnej zostanie odświeżona strona **Przegląd** w celu wyświetlenia zmiany rozmiaru maszyny wirtualnej.

    ![Zmieniono rozmiar maszyny wirtualnej ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrażać maszyny wirtualne na urządzeniu Azure Stack EDGE Pro, zobacz [wdrażanie maszyn wirtualnych za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
