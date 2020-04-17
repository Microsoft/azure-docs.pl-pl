---
title: Konfigurowanie kluczy zarządzanych przez klienta do usługi Import/Eksportowanie za pomocą portalu Azure
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta za pomocą usługi Azure Key Vault dla usługi Azure Import/Export. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456502"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Używanie kluczy zarządzanych przez klienta w usłudze Azure Key Vault do importowania/eksportowania usługi

Usługa Azure Import/Export chroni klucze funkcji BitLocker używane do blokowania dysków za pomocą klucza szyfrowania. Domyślnie klucze funkcji BitLocker są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można również udostępnić klucze zarządzane przez klienta.

Klucze zarządzane przez klienta muszą być tworzone i przechowywane w magazynie kluczy platformy Azure. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)

W tym artykule pokazano, jak używać kluczy zarządzanych przez klienta z usługą importu/eksportu w [witrynie Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

1. Utworzono zadanie importu lub eksportu zgodnie z instrukcjami w:

    - [Tworzenie zadania importu dla obiektów blob](storage-import-export-data-to-blobs.md).
    - [Tworzenie zadania importu dla plików](storage-import-export-data-to-files.md).
    - [Tworzenie zadania eksportu obiektów blob](storage-import-export-data-from-blobs.md)

2. Masz istniejącą usługę Azure Key Vault z kluczem, którego można użyć do ochrony klucza funkcji BitLocker. Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą witryny Azure portal, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](../../key-vault/secrets/quick-create-portal.md).

    - **Usuwanie nietrwałe** i **nie czyścić** są ustawione na istniejącej przechowalni kluczy. Te właściwości nie są domyślnie włączone. Aby włączyć te właściwości, zobacz sekcje **"Włączanie usuwania nietrwałego** i **włączanie ochrony przed przeczyszczaniem"** w jednym z następujących artykułów:

        - [Jak używać soft-delete z programem PowerShell](../../key-vault/general/soft-delete-powershell.md).
        - [Jak używać soft-delete z CLI](../../key-vault/general/soft-delete-cli.md).
    - Istniejący magazyn kluczy powinien mieć klucz RSA o rozmiarze 2048 lub więcej. Aby uzyskać więcej informacji o kluczach, zobacz **Klucze usługi** Azure Key Vault — [informacje o kluczach, wpisach tajnych i certyfikatach usługi Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
    - Magazyn kluczy musi znajdować się w tym samym regionie co konto magazynu danych.  
    - Jeśli nie masz istniejącego usługi Azure Key Vault, możesz również utworzyć ją w linii, zgodnie z opisem w poniższej sekcji.

## <a name="enable-keys"></a>Włączanie klawiszy

Konfigurowanie klucza zarządzanego przez klienta dla usługi importu/eksportu jest opcjonalne. Domyślnie usługa Import/Export używa klucza zarządzanego firmy Microsoft do ochrony klucza funkcji BitLocker. Aby włączyć klucze zarządzane przez klienta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do **bloku Przegląd** zadania importu.
2. W prawym okienku wybierz pozycję **Wybierz sposób szyfrowania kluczy funkcji BitLocker**.

    ![Wybierz opcję szyfrowania](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. W **bloku Szyfrowanie** można wyświetlać i kopiować klucz funkcji BitLocker urządzenia. W obszarze **Typ szyfrowania**możesz wybrać sposób ochrony klucza funkcji BitLocker. Domyślnie używany jest klucz zarządzany firmy Microsoft.

    ![Wyświetl klawisz BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Można określić klucz zarządzany przez klienta. Po wybraniu klucza zarządzanego przez klienta **wybierz przechowalnię kluczy i klucz**.

    ![Wybierz klucz zarządzany przez klienta](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. W **bloku Wybierz klucz z usługi Azure Key Vault** subskrypcja jest automatycznie wypełniana. W przypadku **magazynu kluczy**można wybrać istniejącą przechowalnię kluczy z listy rozwijanej.

    ![Wybieranie lub tworzenie usługi Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Można również wybrać **Utwórz nowy,** aby utworzyć nowy magazyn kluczy. W **bloku Utwórz magazyn kluczy**wprowadź grupę zasobów i nazwę magazynu kluczy. Zaakceptuj wszystkie inne ustawienia domyślne. Wybierz **pozycję Recenzja + Utwórz**.

    ![Tworzenie nowej usługi Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Przejrzyj informacje skojarzone z magazynem kluczy i wybierz pozycję **Utwórz**. Poczekaj kilka minut na ukończenie tworzenia magazynu kluczy.

    ![Tworzenie usługi Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. W **select key from Azure Key Vault**można wybrać klucz w istniejącym magazynie kluczy.

9. Jeśli utworzono nową przechowalnię kluczy, wybierz pozycję **Utwórz nowy,** aby utworzyć klucz. Rozmiar klucza RSA może być 2048 lub większy.

    ![Tworzenie nowego klucza w usłudze Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Jeśli ochrona usuwania nietrwałego i przeczyszczenia nie jest włączona podczas tworzenia magazynu kluczy, magazyn kluczy zostanie zaktualizowany, aby włączono ochronę usuwania nietrwałego i przeczyszcza.

10. Podaj nazwę klucza, zaakceptuj inne ustawienia domyślne i wybierz pozycję **Utwórz**.

    ![Tworzenie nowego klucza](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Wybierz **wersję,** a następnie wybierz pozycję **Wybierz**. Zostaniesz powiadomiony, że klucz jest tworzony w magazynie kluczy.

    ![Nowy klucz utworzony w magazynie kluczy](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

W **bloku Szyfrowanie** można wyświetlić magazyn kluczy i klucz wybrany dla klucza zarządzanego przez klienta.

## <a name="disable-keys"></a>Wyłączanie klawiszy

Można wyłączyć tylko klucze zarządzane firmy Microsoft i przejść do kluczy zarządzanych przez klienta na dowolnym etapie zadania importowania/eksportowania. Nie można jednak wyłączyć klucza zarządzanego przez klienta po jego utworzeniu.

## <a name="troubleshoot-customer-managed-key-errors"></a>Rozwiązywanie problemów z błędami kluczy zarządzanych przez klienta

Jeśli zostanie wyświetlony błąd związany z kluczem zarządzanym przez klienta, użyj poniższej tabeli, aby rozwiązać następujące problemy:

| Kod błędu     |Szczegóły     | Odzyskania?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Zastosowano klucz zarządzany przez klienta, ale dostęp do klucza jest obecnie odwołany. Aby uzyskać więcej informacji, zobacz jak [włączyć dostęp do klucza](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Tak, sprawdź, czy: <ol><li>Magazyn kluczy nadal ma MSI w zasadach dostępu.</li><li>Zasady dostępu zapewniają uprawnienia do Get, Wrap, Unwrap.</li><li>Jeśli magazyn kluczy znajduje się w sieci wirtualnej za zaporą, sprawdź, czy jest włączona **opcja Zezwalaj na zaufane usługi firmy Microsoft.**</li></ol>                                                                                            |
| CmkErrorDisabled ( CmkErrorDisabled )      | Zastosowano klucz zarządzany przez klienta, ale klucz jest wyłączony. Aby uzyskać więcej informacji, zobacz jak [włączyć klucz](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Tak, włączając wersję klucza     |
| CmkErrorNotFound      | Zastosowano klucz zarządzany przez klienta, ale nie można go znaleźć. <br>Jeśli klucz zostanie usunięty i usunięty po upływie okresu przechowywania, nie można odzyskać klucza. Jeśli kopia zapasowa klucza, można przywrócić klucz, aby rozwiązać ten problem. | Nie, klucz został usunięty, a także został usunięty po okresie przechowywania. <br>Tak, tylko wtedy, gdy klient ma kopię zapasową klucza i przywraca go.  |
| CmkErrorVaultNieznajduje | Zastosowano klucz zarządzany przez klienta, ale nie można odnaleźć magazynu kluczy skojarzonego z kluczem.<br>Jeśli magazyn kluczy został usunięty, nie można odzyskać klucza zarządzanego przez klienta.  Jeśli magazyn kluczy został zmigrowany do innej dzierżawy, zobacz [Zmienianie identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji.](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix) |   Nie, jeśli klient usunął magazyn kluczy.<br> Tak, jeśli magazyn kluczy przeszedł migrację dzierżawy, wykonaj jedną z: <ol><li>przenieść z powrotem magazynu kluczy do starego dzierżawy.</li><li>set Identity = None, a następnie z powrotem do tożsamości = SystemAssigned, spowoduje to usunięcie i ponowne utworzenie tożsamości</li></ol>|

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
