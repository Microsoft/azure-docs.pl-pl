---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74566328"
---
W tym artykule założono, że

1. Ustanowiono już połączenie **VPN lokacja-lokacja** lub **Express Route** między siecią lokalną a usługą Azure Virtual Network.
2. Twoje konto użytkownika ma uprawnienia do tworzenia nowej maszyny wirtualnej w ramach subskrypcji platformy Azure, do której maszyny wirtualne zostały awaryjnie przełączone.
3. Twoja subskrypcja obejmuje co najmniej 8 rdzeni, aby można było uruchomić nową maszynę wirtualną serwera przetwarzania.
4. Masz dostępne **hasło serwera konfiguracji**.

> [!TIP]
> Upewnij się, że możesz połączyć się z portem 443 serwera konfiguracji (działającego lokalnie) z usługi Azure Virtual Network, do której maszyny wirtualne zostały awaryjnie przełączone.
