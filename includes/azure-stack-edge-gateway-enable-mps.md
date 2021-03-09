---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102517574"
---
1. Przed rozpoczęciem upewnij się, że:

    1. [Twoje urządzenie Azure Stack EDGE Pro](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) zostało skonfigurowane i aktywowane przy użyciu Azure Stack zasobu brzegowego na platformie Azure.
    1. [Na tym urządzeniu skonfigurowano obliczenia w Azure Portal](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Nawiąż połączenie z interfejsem programu PowerShell](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Użyj poniższego polecenia, aby włączyć pakiety MP na urządzeniu.

    ```powershell
    Start-HcsGpuMPS
    ```