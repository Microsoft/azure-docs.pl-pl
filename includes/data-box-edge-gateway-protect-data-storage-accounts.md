---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75468734"
---
Urządzenie jest skojarzone z kontem magazynu, które jest używane jako miejsce docelowe danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję i 2 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu.

Jeden z tych kluczy jest używany do uwierzytelniania, gdy urządzenie Data Box Edge uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany w rezerwie, dzięki czemu można obrócić klucze okresowo.

Ze względów bezpieczeństwa wiele centrów danych wymaga rotacji kluczy. Zalecamy przestrzeganie następujących najlepszych rozwiązań dotyczących rotacji kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie Chroń klucz konta. Nie Dystrybuuj hasła do innych użytkowników, twardy kod lub Zapisz go w dowolnym miejscu w postaci zwykłego tekstu, który jest dostępny dla innych osób.
- Wygeneruj ponownie klucz konta za pośrednictwem Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../articles/storage/common/storage-account-keys-manage.md).
- Administrator platformy Azure powinien okresowo zmieniać lub ponownie generować klucz podstawowy lub pomocniczy przy użyciu sekcji Magazyn Azure Portal, aby uzyskać bezpośredni dostęp do konta magazynu.