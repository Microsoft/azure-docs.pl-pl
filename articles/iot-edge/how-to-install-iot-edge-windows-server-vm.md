---
title: Uruchom Azure IoT Edge w systemie Windows Server Virtual Machines | Microsoft Docs
description: Instrukcje dotyczące konfiguracji Azure IoT Edge w witrynie Windows Server Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0d8344f5f6b4ccc9f58cc1809f5d625e7e4adf68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494064"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Uruchom Azure IoT Edge w systemie Windows Server Virtual Machines

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury.

Aby dowiedzieć się więcej o tym, jak działa środowisko uruchomieniowe IoT Edge i jakie składniki są uwzględnione, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę uruchamiania środowiska uruchomieniowego Azure IoT Edge na maszynie wirtualnej z systemem Windows Server 2019 przy użyciu oferty [systemu Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace. Postępuj zgodnie z instrukcjami w obszarze [Instalowanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge-windows.md) w systemie Windows, aby używać go z innymi wersjami.

## <a name="deploy-from-the-azure-marketplace"></a>Wdrażanie z poziomu portalu Azure Marketplace

1. Przejdź do oferty [systemu Windows Server](https://www.microsoft.com/cloud-platform/windows-server-pricing) Azure Marketplace lub wyszukiwania "Windows Server" w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Wybierz pozycję **Pobierz teraz**
3. W **planie oprogramowania**Znajdź pozycję "Windows Server 2019 Datacenter Server Core with Containers", a następnie wybierz pozycję **Kontynuuj** w następnym oknie dialogowym.
    * Możesz również użyć tych instrukcji dla innych wersji systemu Windows Server z kontenerami
4. Raz w Azure Portal wybierz pozycję **Utwórz** i postępuj zgodnie z instrukcjami kreatora, aby wdrożyć maszynę wirtualną.
    * Jeśli po raz pierwszy wypróbuje maszynę wirtualną, najłatwiej jest użyć hasła i włączyć protokół RDP i SSH w menu Publiczny port wejściowy.
    * W przypadku obciążeń intensywnie korzystających z zasobów należy uaktualnić rozmiar maszyny wirtualnej, dodając więcej procesorów CPU i/lub pamięć.
5. Po wdrożeniu maszyny wirtualnej skonfiguruj ją w celu nawiązania połączenia z IoT Hub:
    1. Skopiuj parametry połączenia z urządzeniem IoT Edge utworzone w IoT Hub. Zobacz procedurę [pobieranie parametrów połączenia w Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Wybierz nowo utworzony zasób maszyny wirtualnej z Azure Portal i Otwórz opcję **Uruchom polecenie**
    1. Wybierz opcję **RunPowerShellScript**
    1. Skopiuj ten skrypt do okna poleceń z parametrami połączenia urządzenia:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Wykonaj skrypt, aby zainstalować środowisko uruchomieniowe IoT Edge i ustawić parametry połączenia, wybierając pozycję **Uruchom** .
    1. Po minucie lub dwóch powinien zostać wyświetlony komunikat informujący o pomyślnym zainstalowaniu środowiska uruchomieniowego Edge i zainicjowaniu obsługi administracyjnej.

## <a name="deploy-from-the-azure-portal"></a>Wdróż z Azure Portal

1. W Azure Portal Wyszukaj ciąg "Windows Server" i wybierz pozycję **Windows server 2019 Datacenter** , aby rozpocząć przepływ pracy tworzenia maszyny wirtualnej.
2. W obszarze **Wybierz plan oprogramowania** wybierz pozycję "Windows Server 2019 Datacenter Server Core with Containers", a następnie wybierz pozycję **Utwórz** .
3. Ukończ krok 5 w sekcji "wdrażanie z poziomu portalu Azure Marketplace" powyżej.

## <a name="deploy-from-azure-cli"></a>Wdrażanie z poziomu interfejsu wiersza polecenia platformy Azure

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure na pulpicie, Zacznij od zalogowania się:

   ```azurecli-interactive
   az login
   ```

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję, której chcesz użyć:
   1. Wyświetl listę swoich subskrypcji:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Skopiuj pole identyfikatora subskrypcji, którego chcesz użyć
   1. Uruchom to polecenie z skopiowanym IDENTYFIKATORem:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Utwórz nową grupę zasobów (lub określ istniejącą w następnych krokach):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Utwórz nową maszynę wirtualną:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * To polecenie wyświetli monit o podanie hasła, ale można dodać opcję, `--admin-password` Aby łatwiej ją ustawić w skrypcie
   * Obraz systemu Windows Server Core ma obsługę wiersza polecenia tylko z pulpitem zdalnym, więc jeśli chcesz, aby środowisko pulpitu było pełne, określ `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` jako obraz

1. Ustaw parametry połączenia urządzenia (można postępować zgodnie z procedurą [pobierania parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) , jeśli nie masz doświadczenia z tym procesem):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją środowiska uruchomieniowego Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).

Dowiedz się więcej o korzystaniu z maszyn wirtualnych z systemem Windows w [dokumentacji Windows Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/).

Jeśli chcesz użyć protokołu SSH do tej maszyny wirtualnej po zakończeniu instalacji, postępuj zgodnie z instrukcjami [instalacji systemu OpenSSH for Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) , korzystając z pulpitu zdalnego lub zdalnego programu PowerShell.
