---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044690"
---
### <a name="set-up-the-windows-device-portal"></a>Konfigurowanie portalu urządzeń z systemem Windows

Aby nawiązać połączenie z usługą HoloLens za pośrednictwem sieci Wi-Fi, wykonaj następujące kroki:

1. Najpierw [Podłącz urządzenie Hololens do sieci Wi-Fi](https://docs.microsoft.com/hololens/hololens-network).

2. Następnie Uzyskaj adres IP na urządzeniu w obszarze **ustawienia > sieć & Internet > Wi-Fi > zaawansowane opcje**.

3. Z przeglądarki sieci Web na komputerze, przejdź do `https://<YOUR_HOLOLENS_IP_ADDRESS>` . W przeglądarce zostanie wyświetlony następujący komunikat: "Wystąpił problem z certyfikatem zabezpieczeń tej witryny sieci Web". Ten komunikat występuje, ponieważ certyfikat wystawiony dla portalu urządzeń jest certyfikatem z podpisem własnym. Błąd certyfikatu można zignorować i kontynuować.

Zobacz [tutaj](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal) , aby uzyskać więcej informacji na temat konfigurowania portalu urządzeń z systemem Windows.
