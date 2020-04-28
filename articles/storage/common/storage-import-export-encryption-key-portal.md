---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta dla usługi Import/Export
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault usługi Azure Import/Export. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d3e4535c05ef077d14ef74310459a84af0f02fd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176332"
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

        - [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../../key-vault/general/soft-delete-powershell.md).
        - [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../../key-vault/general/soft-delete-cli.md).
    - Istniejący magazyn kluczy powinien mieć klucz RSA o rozmiarze 2048 lub większym. Aby uzyskać więcej informacji na temat kluczy, zobacz **Key Vault klucze** w temacie [Informacje o kluczach, wpisach tajnych i certyfikatach Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - Magazyn kluczy musi znajdować się w tym samym regionie co konto magazynu danych.  
    - Jeśli nie masz istniejącego Azure Key Vault, możesz go również utworzyć w sposób wbudowany zgodnie z opisem w następnej sekcji.

## <a name="enable-keys"></a>Włącz klucze

Konfigurowanie klucza zarządzanego przez klienta dla usługi Import/Export jest opcjonalne. Domyślnie usługa Import/Export używa klucza zarządzanego przez firmę Microsoft w celu ochrony klucza funkcji BitLocker. Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do bloku **Przegląd** zadania importowania.
2. W okienku po prawej stronie wybierz pozycję **Wybierz, w jaki sposób klucze funkcji BitLocker są szyfrowane**.

    ![Wybierz opcję szyfrowania](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. W bloku **szyfrowanie** można wyświetlić i skopiować klucz funkcji BitLocker urządzenia. W obszarze **typ szyfrowania**możesz wybrać sposób ochrony klucza funkcji BitLocker. Domyślnie używany jest klucz zarządzany przez firmę Microsoft.

    ![Wyświetl klucz funkcji BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Możesz określić klucz zarządzany przez klienta. Po wybraniu klucza zarządzanego przez klienta **Wybierz pozycję Magazyn kluczy i klucz**.

    ![Wybierz klucz zarządzany przez klienta](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. W bloku **Wybieranie klucza z Azure Key Vault** subskrypcja zostanie automatycznie wypełniona. W przypadku **magazynu kluczy**można wybrać istniejący magazyn kluczy z listy rozwijanej.

    ![Wybierz lub Utwórz Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Możesz również wybrać pozycję **Utwórz nowy** , aby utworzyć nowy magazyn kluczy. W **bloku Utwórz magazyn kluczy**wprowadź grupę zasobów i nazwę magazynu kluczy. Zaakceptuj wszystkie inne ustawienia domyślne. Wybierz pozycję **Recenzja + Utwórz**.

    ![Utwórz nowy Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Przejrzyj informacje skojarzone z magazynem kluczy i wybierz pozycję **Utwórz**. Poczekaj kilka minut na ukończenie tworzenia magazynu kluczy.

    ![Utwórz Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. W **kluczu select from Azure Key Vault**można wybrać klucz w istniejącym magazynie kluczy.

9. W przypadku utworzenia nowego magazynu kluczy wybierz pozycję **Utwórz nowy** , aby utworzyć klucz. Rozmiar klucza RSA może mieć wartość 2048 lub większą.

    ![Utwórz nowy klucz w Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Jeśli ochrona usuwania nietrwałego i przeczyszczania nie jest włączona podczas tworzenia magazynu kluczy, Magazyn kluczy zostanie zaktualizowany w celu włączenia ochrony trwałego usuwania i przeczyszczania.

10. Podaj nazwę klucza, zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

    ![Utwórz nowy klucz](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Wybierz **wersję** , a następnie wybierz **pozycję Wybierz**. Zostanie wyświetlony monit o utworzenie klucza w magazynie kluczy.

    ![Nowy klucz został utworzony w magazynie kluczy](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

W bloku **szyfrowanie** można zobaczyć Magazyn kluczy i klucz wybrany dla klucza zarządzanego przez klienta.

## <a name="disable-keys"></a>Wyłącz klucze

Klucze zarządzane przez firmę Microsoft można wyłączyć i przenieść do kluczy zarządzanych przez klienta na dowolnym etapie zadania importowania/eksportowania. Nie można jednak wyłączyć klucza zarządzanego przez klienta po jego utworzeniu.

## <a name="troubleshoot-customer-managed-key-errors"></a>Rozwiązywanie problemów z błędami kluczy zarządzanych przez klienta

Jeśli występują błędy związane z kluczem zarządzanym przez klienta, Skorzystaj z poniższej tabeli, aby rozwiązać ten problem:

| Kod błędu     |Szczegóły     | Odzyskiwaln?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Zastosowano klucz zarządzany przez klienta, ale dostęp do klucza jest obecnie odwołany. Aby uzyskać więcej informacji, zobacz jak [włączyć dostęp do klucza](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Tak, sprawdź, czy: <ol><li>Magazyn kluczy ma nadal plik MSI w zasadach dostępu.</li><li>Zasady dostępu zapewniają uprawnienia do pobierania, zawijania i depakowania.</li><li>Jeśli magazyn kluczy znajduje się w sieci wirtualnej za zaporą, sprawdź, czy jest włączona funkcja **Zezwalaj na zaufane usługi firmy Microsoft** .</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Zastosowano klucz zarządzany przez klienta, ale klucz jest wyłączony. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Tak, włączając wersję klucza     |
| CmkErrorKeyNotFound      | Zastosowano klucz zarządzany przez klienta, ale nie można znaleźć magazynu kluczy skojarzonego z kluczem.<br>Jeśli magazyn kluczy został usunięty, nie można odzyskać klucza zarządzanego przez klienta.  Jeśli przeprowadzono migrację magazynu kluczy do innej dzierżawy, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). |   Jeśli magazyn kluczy został usunięty:<ol><li>Tak, jeśli jest to czas trwania funkcji przeczyszczania i ochrony, wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Nie, jeśli przekracza czas trwania ochrony przed usunięciem.</li></ol><br>W przeciwnym razie, jeśli Magazyn kluczy przeszedł do migracji dzierżawy, można go odzyskać, wykonując jedną z poniższych czynności: <ol><li>Przywróć Magazyn kluczy z powrotem do starej dzierżawy.</li><li>Ustaw `Identity = None` , a następnie ustaw wartość z powrotem `Identity = SystemAssigned`na. Spowoduje to usunięcie i odtworzenie tożsamości po utworzeniu nowej tożsamości. Włącz `Get`, `Wrap`i `Unwrap` uprawnienia do nowej tożsamości w zasadach dostępu magazynu kluczy.</li></ol>|

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
