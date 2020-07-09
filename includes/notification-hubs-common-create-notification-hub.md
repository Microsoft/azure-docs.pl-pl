---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081434"
---
### <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień 

W tej sekcji utworzysz centrum powiadomień i skonfigurujesz uwierzytelnianie przy użyciu usługi **APNs**. Można użyć certyfikatu wypychania P12 lub uwierzytelniania opartego na tokenach. Jeśli chcesz użyć już utworzonego centrum powiadomień, możesz przejść do kroku 5.

1. Zaloguj się do [platformy Azure](https://portal.azure.com).

1. Kliknij pozycję **Utwórz zasób**, a następnie wyszukaj i wybierz pozycję **centrum powiadomień**, a następnie kliknij pozycję **Utwórz**.

1. Zaktualizuj następujące pola, a następnie kliknij pozycję **Utwórz**:

    **SZCZEGÓŁY PODSTAWOWE**  

    **Subskrypcja:** Wybierz **subskrypcję** docelową z listy rozwijanej  
    **Grupa zasobów:** Utwórz nową **grupę zasobów** (lub wybierz istniejącą)  

    **SZCZEGÓŁY PRZESTRZENI NAZW**  

    **Przestrzeń nazw centrum powiadomień:** Wprowadź globalnie unikatową nazwę dla obszaru nazw **centrum powiadomień**  

    > [!NOTE]
    > Upewnij się, że dla tego pola wybrana jest opcja **Utwórz nowy** .

    **SZCZEGÓŁY CENTRUM POWIADOMIEŃ**  

    **Centrum powiadomień:** Wprowadź nazwę **centrum powiadomień**  
    **Lokalizacja:** Wybierz odpowiednią lokalizację z listy rozwijanej  
    **Warstwa cenowa:** Pozostaw domyślną opcję **bezpłatna**  

    > [!NOTE]
    > O ile nie osiągnięto maksymalnej liczby centrów w warstwie Bezpłatna.

1. Po zainicjowaniu obsługi administracyjnej **centrum powiadomień** przejdź do tego zasobu.
1. Przejdź do nowego **centrum powiadomień**.
1. Wybierz z listy pozycję **zasady dostępu** (w obszarze **Zarządzaj**).
1. Zanotuj wartości **nazw zasad** wraz z odpowiadającymi im wartościami **parametrów połączenia** .
