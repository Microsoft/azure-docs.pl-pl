---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583064"
---
Urządzenie jest skojarzone z kontem magazynu, które jest używane jako miejsce docelowe danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję oraz dwa 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu.

Jeden z tych kluczy jest używany do uwierzytelniania, gdy urządzenie Azure Stack Edge próbuje uzyskać dostęp do konta magazynu. Drugi klucz jest przechowywany jako rezerwowy, dzięki czemu można wymieniać klucze okresowo.

Ze względów bezpieczeństwa wiele centrów danych wymaga wymiany kluczy. Zalecamy przestrzeganie następujących najlepszych rozwiązań dotyczących rotacji kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie chroń klucz konta. Nie należy rozpowszechniać hasła do innych użytkowników, wykodować go lub zapisywać w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych osób.
- Wygeneruj ponownie klucz konta za pośrednictwem Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../articles/storage/common/storage-account-keys-manage.md).
- Administrator platformy Azure powinien okresowo zmieniać lub ponownie generować klucz podstawowy lub pomocniczy przy użyciu sekcji Magazyn Azure Portal, aby uzyskać bezpośredni dostęp do konta magazynu.