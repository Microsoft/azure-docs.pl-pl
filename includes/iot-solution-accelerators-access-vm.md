---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e3eed0e7378f513e45e0eb9ac719a92e87ec1d53
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026169"
---
## <a name="access-the-virtual-machine"></a>Uzyskaj dostęp do maszyny wirtualnej

Poniższe kroki używają interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell. Jeśli wolisz, możesz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na swoim komputerze deweloperskim i uruchomić polecenie lokalnie.

Poniższe kroki pokazują, jak skonfigurować maszynę wirtualną platformy Azure w celu zezwalania na dostęp za pomocą protokołu **SSH** . W wyświetlonych krokach przyjęto założenie, że wybrana nazwa akceleratora rozwiązania to **contoso-symulacja** — Zamień tę wartość na nazwę wdrożenia:

1. Wyświetl listę zawartości grupy zasobów zawierającej zasoby akceleratora rozwiązań:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Zanotuj nazwę maszyny wirtualnej, publiczny adres IP i grupę zabezpieczeń sieci — te wartości będą potrzebne później.

1. Zaktualizuj grupę zabezpieczeń sieci, aby umożliwić dostęp SSH. Następujące polecenie zakłada, że nazwa sieciowej grupy zabezpieczeń to **contoso-symulacja-sieciowej grupy zabezpieczeń** --zastępuje tę wartość nazwą sieciowej grupy zabezpieczeń:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Włącz dostęp SSH tylko podczas testowania i programowania. W przypadku włączenia protokołu SSH należy [go ponownie wyłączyć najszybciej, jak to możliwe](../articles/security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Zaktualizuj hasło do konta usługi **azureuser** na maszynie wirtualnej, aby poznać hasło, które znasz. Po uruchomieniu następującego polecenia wybierz własne hasło:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Znajdź publiczny adres IP maszyny wirtualnej. Następujące polecenie zakłada, że nazwa maszyny wirtualnej to **VM-vikxv** --zastępuje tę wartość nazwą maszyny wirtualnej, która została wcześniej zanotowana:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Zanotuj publiczny adres IP swojej maszyny wirtualnej.