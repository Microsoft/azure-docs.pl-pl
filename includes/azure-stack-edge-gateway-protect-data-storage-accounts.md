---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285205"
---
Urządzenie jest skojarzone z kontem magazynu, które jest używane jako miejsce docelowe danych na platformie Azure. Dostęp do konta magazynu jest kontrolowany przez subskrypcję oraz dwa 512-bitowe klucze dostępu do magazynu skojarzone z tym kontem magazynu.

Jeden z tych kluczy jest używany do uwierzytelniania, gdy urządzenie Azure Stack Edge próbuje uzyskać dostęp do konta magazynu. Drugi klucz jest przechowywany jako rezerwowy, dzięki czemu można wymieniać klucze okresowo.

Ze względów bezpieczeństwa wiele centrów danych wymaga wymiany kluczy. Zalecamy przestrzeganie następujących najlepszych rozwiązań dotyczących rotacji kluczy:

- Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Starannie chroń klucz konta. Nie udostępniaj hasła innym użytkownikom, nie umieszczaj go trwale w kodzie ani nie zapisuj w postaci zwykłego tekstu, który jest dostępny dla innych osób.
- [Wygeneruj ponownie klucz konta](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) za pośrednictwem Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.
- Administrator platformy Azure powinien okresowo zmieniać lub ponownie generować klucz podstawowy lub pomocniczy przy użyciu sekcji Magazyn Azure Portal, aby uzyskać bezpośredni dostęp do konta magazynu.
- Możesz również użyć własnego klucza szyfrowania do ochrony danych na koncie usługi Azure Storage. Gdy określisz klucz zarządzany przez klienta, ten klucz będzie używany w celu ochrony i kontroli dostępu do klucza szyfrującego dane. Aby uzyskać więcej informacji na temat zabezpieczania danych, zobacz [Włączanie kluczy zarządzanych przez klienta dla konta usługi Azure Storage](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
