---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105126"
---
### <a name="set-up-the-windows-device-portal"></a>Konfigurowanie portalu urządzeń z systemem Windows

Aby nawiązać połączenie z usługą HoloLens za pośrednictwem sieci Wi-Fi, wykonaj następujące kroki:

1. Najpierw [Podłącz urządzenie Hololens do sieci Wi-Fi](/hololens/hololens-network).

2. Następnie Uzyskaj adres IP na urządzeniu w obszarze **ustawienia > sieć & Internet > Wi-Fi > zaawansowane opcje**.

3. Z przeglądarki sieci Web na komputerze, przejdź do `https://<YOUR_HOLOLENS_IP_ADDRESS>` . W przeglądarce zostanie wyświetlony następujący komunikat: "Wystąpił problem z certyfikatem zabezpieczeń tej witryny sieci Web". Ten komunikat występuje, ponieważ certyfikat wystawiony dla portalu urządzeń jest certyfikatem z podpisem własnym. Błąd certyfikatu można zignorować i kontynuować.

Zobacz [tutaj](/windows/mixed-reality/using-the-windows-device-portal) , aby uzyskać więcej informacji na temat konfigurowania portalu urządzeń z systemem Windows.