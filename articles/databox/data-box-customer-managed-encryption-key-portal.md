---
title: Użyj Azure Portal, aby skonfigurować klucze zarządzane przez klienta dla Azure Data Box
description: Dowiedz się, jak za pomocą Azure Portal skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault dla Azure Data Box. Klucze zarządzane przez klienta umożliwiają tworzenie, obracanie, wyłączanie i odwoływanie kontroli dostępu.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 31147d534109e0d74d33d102075c69eeb703496e
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739939"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Użyj kluczy zarządzanych przez klienta w Azure Key Vault Azure Data Box

Azure Data Box chroni klucz odblokowywania urządzenia (znany również jako hasło urządzenia), który jest używany do blokowania urządzenia za pomocą klucza szyfrowania. Domyślnie klucz odblokowania urządzenia dla zamówienia urządzenie Data Box jest szyfrowany przy użyciu klucza zarządzanego przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczem odblokowywania urządzenia, możesz również podać klucz zarządzany przez klienta. 

Klucze zarządzane przez klienta muszą być tworzone i przechowywane w Azure Key Vault. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md).

W tym artykule pokazano, jak używać kluczy zarządzanych przez klienta Azure Data Box w [Azure Portal](https://portal.azure.com/). Ten artykuł dotyczy zarówno urządzeń Azure Data Box, jak i urządzeń Azure Data Box Heavy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że spełniono następujące warunki:

1. Utworzono zamówienie Azure Data Box zgodnie z instrukcjami podanymi w [samouczku: order Azure Data Box](data-box-deploy-ordered.md).

2. Masz już istniejące Azure Key Vault z kluczem, którego możesz użyć do ochrony klucza odblokowywania urządzenia. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../key-vault/secrets/quick-create-portal.md).

    - **Usuwanie nietrwałe** i **nie przeczyszczania** jest ustawione dla istniejącego magazynu kluczy. Te właściwości nie są domyślnie włączone. Aby włączyć te właściwości, zobacz sekcję zatytułowaną **Włączanie usuwania nietrwałego** i **Włączanie ochrony przed przeczyszczeniem** w jednym z następujących artykułów:

        - [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../key-vault/general/soft-delete-powershell.md).
        - [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia](../key-vault/general/soft-delete-cli.md).
    - Istniejący magazyn kluczy powinien mieć klucz RSA o rozmiarze 2048 lub większym. Aby uzyskać więcej informacji na temat kluczy, zobacz [Informacje o kluczach Azure Key Vault](../key-vault/keys/about-keys.md).
    - Magazyn kluczy musi znajdować się w tym samym regionie co konta magazynu używane na potrzeby danych. Z zasobem Azure Data Box można połączyć wiele kont magazynu.
    - Jeśli nie masz istniejącego magazynu kluczy, możesz go również utworzyć w sposób wbudowany zgodnie z opisem w poniższej sekcji.

## <a name="enable-keys"></a>Włącz klucze

Skonfigurowanie klucza zarządzanego przez klienta dla Azure Data Box jest opcjonalne. Domyślnie do ochrony klucza funkcji BitLocker urządzenie Data Box jest używany klucz zarządzany przez firmę Microsoft. Aby włączyć klucz zarządzany przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do bloku **Przegląd** dla zamówienia urządzenie Data Box.

    ![Blok przeglądu urządzenie Data Box kolejności](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Przejdź do pozycji **ustawienia > szyfrowanie**. W obszarze **typ szyfrowania**możesz wybrać sposób ochrony klucza odblokowywania urządzenia. Domyślnie klucz zarządzany przez firmę Microsoft jest używany do ochrony hasła odblokowywania urządzenia. 

    ![Wybierz opcję szyfrowania](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Wybierz typ szyfrowania jako **klucz zarządzany przez klienta**. Po wybraniu klucza zarządzanego przez klienta **Wybierz magazyn kluczy i klucz**.

    ![Wybierz klucz zarządzany przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. W bloku **Wybieranie klucza z Azure Key Vault** subskrypcja zostanie automatycznie wypełniona. W przypadku **magazynu kluczy**można wybrać istniejący magazyn kluczy z listy rozwijanej.

    ![Utwórz nowy Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Możesz również wybrać pozycję **Utwórz nowy** , aby utworzyć nowy magazyn kluczy. W **bloku Utwórz magazyn kluczy**wprowadź grupę zasobów i nazwę magazynu kluczy. Upewnij się, że ochrona **usuwania nietrwałego** i **przeczyszczania** jest włączona. Zaakceptuj wszystkie inne ustawienia domyślne. Wybierz pozycję **Recenzja + Utwórz**.

    ![Utwórz nową Azure Key Vault 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Przejrzyj informacje skojarzone z magazynem kluczy i wybierz pozycję **Utwórz**. Poczekaj kilka minut na ukończenie tworzenia magazynu kluczy.

    ![Utwórz Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. W **kluczu select from Azure Key Vault**można wybrać klucz w istniejącym magazynie kluczy.

    ![Utwórz nowy klucz w Azure Key Vault 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Jeśli chcesz utworzyć nowy klucz, wybierz pozycję **Utwórz nowy** , aby utworzyć klucz. Rozmiar klucza RSA może mieć wartość 2048 lub większą.

    ![Utwórz nowy klucz w Azure Key Vault 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Podaj nazwę klucza, zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**. 

    ![Utwórz nowy klucz](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Zostanie wyświetlony monit o utworzenie klucza w magazynie kluczy. Wybierz **wersję** , a następnie wybierz **pozycję Wybierz**.

    ![Nowy klucz został utworzony w magazynie kluczy](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. W okienku **typ szyfrowania** można zobaczyć Magazyn kluczy i klucz wybrany dla klucza zarządzanego przez klienta.

    ![Klucz i Magazyn kluczy dla klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Zapisz klucz. 

    ![Zapisz klucz zarządzany przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Adres URL klucza jest wyświetlany w obszarze **typ szyfrowania**.

    ![Adres URL klucza zarządzanego przez klienta](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Klucz zarządzany przez firmę Microsoft można wyłączyć i przenieść do klucza zarządzanego przez klienta na dowolnym etapie kolejności urządzenie Data Box. Jednak po utworzeniu klucza zarządzanego przez klienta nie można przełączyć się z powrotem do klucza zarządzanego przez firmę Microsoft.

## <a name="troubleshoot-errors"></a>Rozwiązywanie problemów

Jeśli otrzymasz jakiekolwiek błędy związane z kluczem zarządzanym przez klienta, Skorzystaj z poniższej tabeli, aby rozwiązać problem.

| Kod błędu| Szczegóły błędu| Odzyskiwaln?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Nie można pobrać klucza dostępu, ponieważ klucz zarządzany przez klienta jest wyłączony.| Tak, włączając wersję klucza.|
| SsemUserErrorEncryptionKeyExpired| Nie można pobrać klucza dostępu, ponieważ klucz zarządzany przez klienta wygasł.| Tak, włączając wersję klucza.|
| SsemUserErrorKeyDetailsNotFound| Nie można pobrać klucza dostępu, ponieważ nie można odnaleźć klucza zarządzanego przez klienta.| Jeśli magazyn kluczy został usunięty, nie można odzyskać klucza zarządzanego przez klienta.  Jeśli przeprowadzono migrację magazynu kluczy do innej dzierżawy, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../key-vault/general/move-subscription.md). Jeśli magazyn kluczy został usunięty:<ol><li>Tak, jeśli jest to czas trwania funkcji przeczyszczania i ochrony, wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nie, jeśli przekracza czas trwania ochrony przed usunięciem.</li></ol><br>W przeciwnym razie, jeśli Magazyn kluczy przeszedł do migracji dzierżawy, można go odzyskać, wykonując jedną z poniższych czynności: <ol><li>Przywróć Magazyn kluczy z powrotem do starej dzierżawy.</li><li>Ustaw `Identity = None` , a następnie ustaw wartość z powrotem na `Identity = SystemAssigned` . Spowoduje to usunięcie i odtworzenie tożsamości po utworzeniu nowej tożsamości. Włącz `Get` , `Wrap` i `Unwrap` uprawnienia do nowej tożsamości w zasadach dostępu magazynu kluczy.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Nie można pobrać klucza dostępu, ponieważ odwołuje się do niego dostęp do klucza zarządzanego przez klienta.| Tak, sprawdź, czy: <ol><li>Magazyn kluczy ma nadal plik MSI w zasadach dostępu.</li><li>Zasady dostępu zapewniają uprawnienia do pobierania, zawijania i depakowania.</li><li>Jeśli magazyn kluczy znajduje się w sieci wirtualnej za zaporą, sprawdź, czy jest włączona funkcja **Zezwalaj na zaufane usługi firmy Microsoft** .</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Nie można pobrać klucza dostępu, ponieważ nie można znaleźć skojarzonego magazynu kluczy dla klucza zarządzanego przez klienta. | Jeśli magazyn kluczy został usunięty, nie można odzyskać klucza zarządzanego przez klienta.  Jeśli przeprowadzono migrację magazynu kluczy do innej dzierżawy, zobacz [zmiana identyfikatora dzierżawy magazynu kluczy po przeniesieniu subskrypcji](../key-vault/general/move-subscription.md). Jeśli magazyn kluczy został usunięty:<ol><li>Tak, jeśli jest to czas trwania funkcji przeczyszczania i ochrony, wykonaj kroki opisane w sekcji [odzyskiwanie magazynu kluczy](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nie, jeśli przekracza czas trwania ochrony przed usunięciem.</li></ol><br>W przeciwnym razie, jeśli Magazyn kluczy przeszedł do migracji dzierżawy, można go odzyskać, wykonując jedną z poniższych czynności: <ol><li>Przywróć Magazyn kluczy z powrotem do starej dzierżawy.</li><li>Ustaw `Identity = None` , a następnie ustaw wartość z powrotem na `Identity = SystemAssigned` . Spowoduje to usunięcie i odtworzenie tożsamości po utworzeniu nowej tożsamości. Włącz `Get` , `Wrap` i `Unwrap` uprawnienia do nowej tożsamości w zasadach dostępu magazynu kluczy.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Nie można pobrać klucza dostępu, ponieważ nie można odnaleźć klucza zarządzanego przez klienta.| Tak, sprawdź, czy: <ol><li>Magazyn kluczy ma nadal plik MSI w zasadach dostępu.</li><li>Tożsamość jest przypisana do systemu typu.</li><li>Włącz uprawnienia Get, zawijania i depakowania do tożsamości w zasadach dostępu magazynu kluczy.</li></ol>|
| Błąd rodzajowy  | Nie można pobrać klucza dostępu.| Jest to błąd ogólny. Skontaktuj się pomoc techniczna firmy Microsoft, aby rozwiązać problem z błędem i określić następne kroki.|


## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Key Vault](../key-vault/general/overview.md)?
