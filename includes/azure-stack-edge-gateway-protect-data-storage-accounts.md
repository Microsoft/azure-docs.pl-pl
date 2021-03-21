---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98901095"
---
Urządzenie jest skojarzone z kontem magazynu, które jest używane jako miejsce docelowe danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję oraz dwa 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu.

Jeden z tych kluczy jest używany do uwierzytelniania, gdy urządzenie Data Box Edge uzyskuje dostęp do konta magazynu. Drugi klucz jest przechowywany jako rezerwowy, dzięki czemu można wymieniać klucze okresowo.

Ze względów bezpieczeństwa wiele centrów danych wymaga wymiany kluczy. Zalecamy przestrzeganie następujących najlepszych rozwiązań dotyczących rotacji kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie chroń klucz konta. Nie udostępniaj hasła innym użytkownikom, nie umieszczaj go trwale w kodzie ani nie zapisuj w postaci zwykłego tekstu, który jest dostępny dla innych osób.
- [Wygeneruj ponownie klucz konta](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) za pośrednictwem Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.
- Administrator platformy Azure powinien okresowo zmieniać lub ponownie generować klucz podstawowy lub pomocniczy przy użyciu sekcji Magazyn Azure Portal, aby uzyskać bezpośredni dostęp do konta magazynu.
