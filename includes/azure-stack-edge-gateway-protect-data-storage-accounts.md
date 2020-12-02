---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467805"
---
Urządzenie jest skojarzone z kontem magazynu, które jest używane jako miejsce docelowe danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję oraz dwa 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu.

Jeden z tych kluczy jest używany do uwierzytelniania, gdy urządzenie Data Box Edge uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany jako rezerwowy, dzięki czemu można wymieniać klucze okresowo.

Ze względów bezpieczeństwa wiele centrów danych wymaga wymiany kluczy. Zalecamy przestrzeganie następujących najlepszych rozwiązań dotyczących rotacji kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie chroń klucz konta. Nie udostępniaj hasła innym użytkownikom, nie umieszczaj go trwale w kodzie ani nie zapisuj w postaci zwykłego tekstu, który jest dostępny dla innych osób.
- [Wygeneruj ponownie klucz konta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) za pośrednictwem Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.
- Administrator platformy Azure powinien okresowo zmieniać lub ponownie generować klucz podstawowy lub pomocniczy przy użyciu sekcji Magazyn Azure Portal, aby uzyskać bezpośredni dostęp do konta magazynu.