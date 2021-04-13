---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305564"
---
Upewnij się, że w celu uzyskania dostępu do urządzenia z poziomu klienta można użyć następujących kroków.

Sprawdź, czy klient może połączyć się z lokalną Azure Resource Manager. 

1. Wywołaj interfejsy API urządzeń lokalnych w celu uwierzytelnienia:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Podaj nazwę użytkownika `EdgeArmUser` i hasło, aby połączyć się za pośrednictwem Azure Resource Manager. Jeśli nie odwołujesz hasła, [zresetuj hasło dla Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) i Użyj tego hasła do logowania.