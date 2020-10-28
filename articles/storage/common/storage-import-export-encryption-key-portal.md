---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta dla usługi Import/Export
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault usługi Azure Import/Export. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4362b579b7f01570a2b5fd072bf53ad495797cd8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783780"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Korzystanie z kluczy zarządzanych przez klienta w Azure Key Vault dla usługi Import/Export

Usługa Azure Import/Export chroni klucze funkcji BitLocker używane do blokowania dysków za pomocą klucza szyfrowania. Domyślnie klucze funkcji BitLocker są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można również udostępnić klucze zarządzane przez klienta.

Klucze zarządzane przez klienta muszą być tworzone i przechowywane w Azure Key Vault. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md)

W tym artykule pokazano, jak używać kluczy zarządzanych przez klienta w usłudze Import/Export w [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

1. Utworzono zadanie importowania lub eksportowania zgodnie z instrukcjami w temacie:

    - [Utwórz zadanie importu dla obiektów BLOB](storage-import-export-data-to-blobs.md).
    - [Utwórz zadanie importu dla plików](storage-import-export-data-to-files.md).
    - [Tworzenie zadania eksportu dla obiektów BLOB](storage-import-export-data-from-blobs.md)

2. Masz już istniejące Azure Key Vault z kluczem, który służy do ochrony klucza funkcji BitLocker. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../../key-vault/secrets/quick-create-portal.md).

    - **Usuwanie nietrwałe** i **nie przeczyszczanie** jest ustawione na istniejącym Key Vault. Te właściwości nie są domyślnie włączone. Aby włączyć te właściwości, zobacz sekcję zatytułowaną **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczeniem** w jednym z następujących artykułów:

        - [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/key-vault-recovery.md).
        - [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/key-vault-recovery.md).
    - Istniejący magazyn kluczy powinien mieć klucz RSA o rozmiarze 2048 lub większym. Aby uzyskać więcej informacji na temat kluczy, zobacz [Informacje o kluczach](../../key-vault/keys/about-keys.md).
    - Magazyn kluczy musi znajdować się w tym samym regionie co konto magazynu danych.  
    - Jeśli nie masz istniejącego Azure Key Vault, możesz go również utworzyć w sposób wbudowany zgodnie z opisem w następnej sekcji.

## <a name="enable-keys"></a>Włącz klucze

Konfigurowanie klucza zarządzanego przez klienta dla usługi Import/Export jest opcjonalne. Domyślnie usługa Import/Export używa klucza zarządzanego przez firmę Microsoft w celu ochrony klucza funkcji BitLocker. Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do bloku **Przegląd** zadania importowania.
2. W okienku po prawej stronie wybierz pozycję **Wybierz, w jaki sposób klucze funkcji BitLocker są szyfrowane** .

    ![Wybierz opcję szyfrowania](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. W bloku **szyfrowanie** można wyświetlić i skopiować klucz funkcji BitLocker urządzenia. W obszarze **typ szyfrowania** możesz wybrać sposób ochrony klucza funkcji BitLocker. Domyślnie używany jest klucz zarządzany przez firmę Microsoft.

    ![Wyświetl klucz funkcji BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Możesz określić klucz zarządzany przez klienta. Po wybraniu klucza zarządzanego przez klienta **Wybierz pozycję Magazyn kluczy i klucz** .

    ![Wybierz klucz zarządzany przez klienta](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. W bloku **Wybieranie klucza z Azure Key Vault** subskrypcja zostanie automatycznie wypełniona. W przypadku **magazynu kluczy** można wybrać istniejący magazyn kluczy z listy rozwijanej.

    ![Wybierz lub Utwórz Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Możesz również wybrać pozycję **Utwórz nowy** , aby utworzyć nowy magazyn kluczy. W **bloku Utwórz magazyn kluczy** wprowadź grupę zasobów i nazwę magazynu kluczy. Zaakceptuj wszystkie inne ustawienia domyślne. Wybierz pozycję **Recenzja + Utwórz** .

    ![Utwórz nowy Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Przejrzyj informacje skojarzone z magazynem kluczy i wybierz pozycję **Utwórz** . Poczekaj kilka minut na ukończenie tworzenia magazynu kluczy.

    ![Utwórz Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. W **kluczu select from Azure Key Vault** można wybrać klucz w istniejącym magazynie kluczy.

9. W przypadku utworzenia nowego magazynu kluczy wybierz pozycję **Utwórz nowy** , aby utworzyć klucz. Rozmiar klucza RSA może mieć wartość 2048 lub większą.

    ![Utwórz nowy klucz w Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Jeśli ochrona usuwania nietrwałego i przeczyszczania nie jest włączona podczas tworzenia magazynu kluczy, Magazyn kluczy zostanie zaktualizowany w celu włączenia ochrony trwałego usuwania i przeczyszczania.

10. Podaj nazwę klucza, zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz** .

    ![Utwórz nowy klucz](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Wybierz **wersję** , a następnie wybierz **pozycję Wybierz** . Zostanie wyświetlony monit o utworzenie klucza w magazynie kluczy.

    ![Nowy klucz został utworzony w magazynie kluczy](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

W bloku **szyfrowanie** można zobaczyć Magazyn kluczy i klucz wybrany dla klucza zarządzanego przez klienta.

> [!IMPORTANT]
> Klucze zarządzane przez firmę Microsoft można wyłączyć i przenieść do kluczy zarządzanych przez klienta na dowolnym etapie zadania importowania/eksportowania. Nie można jednak wyłączyć klucza zarządzanego przez klienta po jego utworzeniu.

## <a name="troubleshoot-customer-managed-key-errors"></a>Rozwiązywanie problemów z błędami kluczy zarządzanych przez klienta

Jeśli występują błędy związane z kluczem zarządzanym przez klienta, Skorzystaj z poniższej tabeli, aby rozwiązać ten problem:

| Kod błędu     |Szczegóły     | Odzyskiwaln?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Dostęp do klucza zarządzanego przez klienta został odwołany.                                                       | Tak, sprawdź, czy: <ol><li>Magazyn kluczy ma nadal plik MSI w zasadach dostępu.</li><li>Zasady dostępu mają włączone uprawnienia pobieranie, zawijanie i odpakowywanie.</li><li>Jeśli magazyn kluczy znajduje się w sieci wirtualnej za zaporą, sprawdź, czy jest włączona funkcja **Zezwalaj na zaufane usługi firmy Microsoft** .</li><li>Sprawdź, czy plik MSI zasobu zadania został zresetowany do `None` korzystania z interfejsów API.<br>Jeśli tak, a następnie ustaw wartość z powrotem na `Identity = SystemAssigned` . Spowoduje to odtworzenie tożsamości zasobu zadania.<br>Po utworzeniu nowej tożsamości, włączeniu i przypisaniu `Get` `Wrap` `Unwrap` nowej tożsamości w zasadach dostępu magazynu kluczy</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Klucz zarządzany przez klienta jest wyłączony.                                         | Tak, włączając wersję klucza     |
| CmkErrorKeyNotFound      | Nie można znaleźć klucza zarządzanego przez klienta. | Tak, jeśli klucz został usunięty, ale nadal trwa przeczyszczanie, za pomocą polecenia [Cofnij usuwanie klucza magazynu kluczy](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Przejmi <ol><li>Tak, jeśli klient ma klucz z kopią zapasową i przywraca go.</li><li>Nie, w przeciwnym razie.</li></ol>
| CmkErrorVaultNotFound |Nie można odnaleźć magazynu kluczy zarządzanego przez klienta. |   Jeśli magazyn kluczy został usunięty:<ol><li>Tak, jeśli jest to czas trwania funkcji przeczyszczania i ochrony, wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](../../key-vault/general/soft-delete-overview.md#key-vault-recovery).</li><li>Nie, jeśli przekracza czas trwania ochrony przed usunięciem.</li></ol><br>Jeśli magazyn kluczy został zmigrowany do innej dzierżawy, można go odzyskać przy użyciu jednego z poniższych kroków:<ol><li>Przywróć Magazyn kluczy z powrotem do starej dzierżawy.</li><li>Ustaw `Identity = None` , a następnie ustaw wartość z powrotem na `Identity = SystemAssigned` . Spowoduje to usunięcie i odtworzenie tożsamości po utworzeniu nowej tożsamości. Włącz `Get` , `Wrap` i `Unwrap` uprawnienia do nowej tożsamości w zasadach dostępu magazynu kluczy.</li></ol>|

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Key Vault](../../key-vault/general/overview.md)?