---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763436"
---
Przed wdrożeniem maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge musisz skonfigurować klienta programu tak, aby łączył się z urządzeniem za pośrednictwem Azure Resource Manager przez Azure PowerShell. Aby uzyskać szczegółowe instrukcje, przejdź do pozycji [Połącz z Azure Resource Manager na urządzeniu brzegowym Azure Stack](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Upewnij się, że w celu uzyskania dostępu do urządzenia z poziomu klienta można użyć następujących kroków (Ta konfiguracja została wykonana podczas nawiązywania połączenia z usługą Azure Resource Manager, sprawdzasz, czy konfiguracja zakończyła się pomyślnie). 

1. Sprawdź, czy Azure Resource Manager komunikacja działa. Wpisz:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Wywoływanie interfejsów API urządzeń lokalnych w celu uwierzytelnienia. Wpisz: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Podaj nazwę użytkownika- *EdgeARMuser* i hasło, aby połączyć się za pośrednictwem Azure Resource Manager.

1. W przypadku skonfigurowania **obliczeń** dla Kubernetes można pominąć ten krok. Upewnij się, że włączono interfejs sieciowy dla obliczeń. W lokalnym interfejsie użytkownika przejdź do pozycji ustawienia **obliczeń** . Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego. Utworzone maszyny wirtualne zostaną podłączone do przełącznika wirtualnego dołączonego do tego portu i skojarzonej sieci. Upewnij się, że wybrano sieć zgodną z adresem IP, który będzie używany przez maszynę wirtualną.  

    ![Włącz ustawienia obliczeń 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Włącz obliczenia w interfejsie sieciowym. Azure Stack Edge spowoduje utworzenie przełącznika wirtualnego i zarządzanie nim za pomocą tego interfejsu sieciowego. W tej chwili nie należy wprowadzać określonych adresów IP dla usługi Kubernetes. Włączenie obliczeń może potrwać kilka minut.

    > [!NOTE]
    > W przypadku tworzenia maszyn wirtualnych GPU wybierz interfejs sieciowy połączony z Internetem. Pozwala to na zainstalowanie rozszerzenia procesora GPU na urządzeniu.


1. Włącz rolę maszyny wirtualnej na podstawie Azure Portal. Ten krok polega na utworzeniu unikatowej subskrypcji urządzenia służącego do tworzenia maszyn wirtualnych za pośrednictwem lokalnych interfejsów API urządzenia. 

    1. Aby włączyć rolę maszyny wirtualnej, w Azure Portal przejdź do zasobu Azure Stack Edge dla urządzenia Azure Stack Edge. Przejdź do obszaru **obliczeniowego Edge > Virtual Machines**.

        ![Dodaj obraz maszyny wirtualnej 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Wybierz pozycję **Virtual Machines** , aby przejść do strony **Przegląd** . **Włącz** zarządzanie chmurą maszyn wirtualnych.
        ![Dodaj obraz maszyny wirtualnej 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)