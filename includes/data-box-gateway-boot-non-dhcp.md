---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582733"
---
W przypadku rozruchu w środowisku innym niż DHCP wykonaj następujące kroki, aby wdrożyć maszynę wirtualną dla Data Box Gateway.

1. [Połącz się z interfejsem programu Windows PowerShell dla urządzenia](#connect-to-the-powershell-interface).
2. Użyj `Get-HcsIpAddress` polecenia cmdlet, aby wyświetlić listę interfejsów sieciowych włączonych na urządzeniu wirtualnym. Jeśli urządzenie ma włączony jeden interfejs sieciowy, jego domyślną nazwą jest `Ethernet`.

    W poniższym przykładzie pokazano użycie tego polecenia cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Skonfiguruj sieć za pomocą polecenia cmdlet `Set-HcsIpAddress`. Zobacz poniższy przykład:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

