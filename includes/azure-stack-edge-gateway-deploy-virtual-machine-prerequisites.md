---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500221"
---
Przed wdrożeniem maszyn wirtualnych na urządzeniu z systemem Azure Stack Edge musisz skonfigurować klienta programu tak, aby łączył się z urządzeniem za pośrednictwem Azure Resource Manager przez Azure PowerShell. Aby uzyskać szczegółowe instrukcje, przejdź do pozycji [Połącz z Azure Resource Manager na urządzeniu brzegowym Azure Stack](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Upewnij się, że w celu uzyskania dostępu do urządzenia z poziomu klienta można użyć następujących kroków. (Ta konfiguracja została wykonana podczas nawiązywania połączenia z Azure Resource Manager. Teraz wystarczy sprawdzić, czy konfiguracja zakończyła się pomyślnie. 

1. Sprawdź, czy Azure Resource Manager komunikacja działa. Wprowadź:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Wywoływanie interfejsów API urządzeń lokalnych w celu uwierzytelnienia. Wprowadź: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Podaj nazwę użytkownika- *EdgeARMuser* i hasło, aby połączyć się za pośrednictwem Azure Resource Manager.

1. W przypadku skonfigurowania **obliczeń** dla Kubernetes można pominąć ten krok. Upewnij się, że włączono interfejs sieciowy dla obliczeń. W lokalnym interfejsie użytkownika przejdź do pozycji ustawienia **obliczeń** . Wybierz interfejs sieciowy, który ma zostać użyty do utworzenia przełącznika wirtualnego. Utworzone maszyny wirtualne zostaną podłączone do przełącznika wirtualnego dołączonego do tego portu i skojarzonej sieci. Upewnij się, że wybrano sieć zgodną z adresem IP, który będzie używany przez maszynę wirtualną.  

    ![Zrzut ekranu pokazujący, jak włączyć ustawienia obliczeń.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Włącz obliczenia w interfejsie sieciowym. Azure Stack Edge spowoduje utworzenie przełącznika wirtualnego i zarządzanie nim za pomocą tego interfejsu sieciowego. W tej chwili nie wprowadzaj określonych adresów IP dla usługi Kubernetes. Włączenie obliczeń może potrwać kilka minut.

    > [!NOTE]
    > Jeśli tworzysz maszyny wirtualne GPU, wybierz interfejs sieciowy połączony z Internetem. Pozwala to na zainstalowanie rozszerzenia procesora GPU na urządzeniu.


