---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013279"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Ustawianie szyfrowania na koncie magazynu

1. W Azure Portal wprowadź nazwę konta magazynu, które chcesz zaszyfrować w polu **wyszukiwania** w górnej części ekranu.  Dopasowania będą wyświetlane poniżej pola wyszukiwania.
1. Wybierz konto magazynu, którego szukasz. Zostanie wyświetlony ekran konto magazynu.
1. Wybierz pozycję **szyfrowanie**.
1. Wybierz pozycję klucze zarządzane przez firmę Microsoft lub klucze zarządzane przez klienta.

### <a name="use-microsoft-managed-keys"></a>Użyj kluczy zarządzanych przez firmę Microsoft

Domyślnie dane na koncie magazynu są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft.

### <a name="use-customer-managed-keys"></a>Korzystanie z kluczy zarządzanych przez klienta

1. Wybierz pozycję **klucze zarządzane przez klienta**.
1. Wybierz opcję **Wprowadź identyfikator URI klucza** lub **Wybierz z magazynu kluczy**.
    1. W przypadku wybrania opcji **Wprowadź identyfikator URI klucza** wprowadź identyfikator URI klucza w polu Identyfikator URI klucza i wybierz subskrypcję. (Może być już wybrane dla Ciebie).
    1. W przypadku wybrania opcji **Wybierz z magazynu kluczy** wybierz pozycję **Wybierz magazyn kluczy i klucz**. Zostanie wyświetlony ekran Wybieranie klucza z Azure Key Vault.
1. Wybierz **Key Vault** , którego chcesz użyć, a następnie wybierz klucz, który znajduje się już w magazynie kluczy lub **Utwórz nowy klucz**.
    1. Jeśli zdecydujesz się utworzyć nowy klucz, wybierz opcję **Generuj** lub **Importuj** z listy rozwijanej **Opcje** . Można importować tylko klucze RSA.
    1. Aby wygenerować nowy klucz, nadaj kluczowi nazwę w polu **Nazwa** , a następnie wybierz typ klucza:
        1. Rozmiary kluczy RSA: 2048, 3072 lub 4096. Jest to najczęściej wybieranych klientów.
        1. Nazwy krzywych we-eliptyczna: P-256, P-384, P-521 lub P-256 K
        1. Opcjonalnie można ustawić daty aktywacji i wygaśnięcia klucza.
        1. Wybierz pozycję **tak** , aby włączyć automatyczne obracanie kluczy.
        1. Wybierz pozycję **Utwórz**.
    1. Aby zaimportować klucz, wybierz plik do przekazania, klikając w dowolnym miejscu w **polu Wybierz plik**.
        1. Podaj nazwę klucza w polu **Nazwa** .
        1. Opcjonalnie można ustawić daty aktywacji i wygaśnięcia klucza.
        1. Wybierz pozycję **tak** , aby włączyć automatyczne obracanie kluczy.
        1. Wybierz pozycję **Utwórz**.
    1. Wybierz pozycję **Wybierz** , aby wybrać ten klucz, aby zaszyfrować konto magazynu. Nastąpi powrót do ekranu szyfrowania.
1. **WAŻNE!** Wybierz pozycję **Zapisz** , aby zapisać ustawienia szyfrowania lub wszystkie dane zostały utracone.
