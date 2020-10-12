---
title: Szyfrowanie danych — Azure Database for MySQL Azure Portal
description: Dowiedz się, jak skonfigurować szyfrowanie danych i zarządzać nimi Azure Database for MySQL przy użyciu Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 201459f4a7d2d23b384435493d6272e569698933
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887172"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Szyfrowanie danych dla Azure Database for MySQL przy użyciu Azure Portal

Dowiedz się, jak za pomocą Azure Portal skonfigurować szyfrowanie danych dla Azure Database for MySQL i zarządzać nimi.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* W Azure Key Vault Utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta.
* Magazyn kluczy musi mieć następujące właściwości, które mają być używane jako klucz zarządzany przez klienta:
  * [Usuwanie nietrwałe](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Przeczyść chronione](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty do użycia jako klucz zarządzany przez klienta:
  * Brak daty wygaśnięcia
  * Niewyłączone
  * Możliwość wykonania operacji get, zawijania klucza, odpakowania klucza

## <a name="set-the-right-permissions-for-key-operations"></a>Ustaw odpowiednie uprawnienia dla operacji Key

1. W Key Vault wybierz pozycję **zasady dostępu**  >  **Dodaj zasady dostępu**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

2. Wybierz pozycję **uprawnienia**, a następnie wybierz pozycję **Pobierz**, **Zawijaj**, **Odpakuj**oraz **podmiot zabezpieczeń**, który jest nazwą serwera MySQL. Jeśli nie można znaleźć podmiotu zabezpieczeń serwera na liście istniejących podmiotów zabezpieczeń, należy go zarejestrować. Zostanie wyświetlony monit o zarejestrowanie podmiotu zabezpieczeń serwera podczas próby skonfigurowania szyfrowania danych po raz pierwszy i niepowodzenie.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

3. Wybierz pozycję **Zapisz**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ustaw szyfrowanie danych dla Azure Database for MySQL

1. W obszarze Azure Database for MySQL wybierz pozycję **szyfrowanie danych** , aby skonfigurować klucz zarządzany przez klienta.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

2. Można wybrać Magazyn kluczy i parę kluczy lub wprowadzić identyfikator klucza.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

3. Wybierz pozycję **Zapisz**.

4. Aby upewnić się, że wszystkie pliki (w tym pliki tymczasowe) są całkowicie zaszyfrowane, należy ponownie uruchomić serwer.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Korzystanie z szyfrowania danych dla serwerów przywracania lub repliki

Gdy Azure Database for MySQL jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą operacji repliki (lokalnej/obejmującej wiele regionów). W przypadku szyfrowanego serwera MySQL można wykonać następujące czynności, aby utworzyć zaszyfrowany przywrócony serwer.

1. Na serwerze wybierz pozycję **Przegląd**  >  **przywracanie**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

   Lub dla serwera z włączoną replikacją, w obszarze **Ustawienia** wybierz pozycję **replikacja**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

2. Po zakończeniu operacji przywracania tworzony nowy serwer jest szyfrowany przy użyciu klucza serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer jest niedostępny. Zapobiega to manipulowaniu danymi, ponieważ dla nowej tożsamości serwera nie udzielono jeszcze uprawnienia dostępu do magazynu kluczy.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

3. Aby zapewnić dostęp do serwera, ponownie sprawdź poprawność klucza na przywróconym serwerze. Wybierz pozycję **szyfrowanie danych**ponownie  >  **Sprawdź poprawność klucza**.

   > [!NOTE]
   > Pierwsza próba ponownego zweryfikowania zakończy się niepowodzeniem, ponieważ nazwa główna usługi nowego serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, wybierz pozycję **Sprawdź ponownie klucz**, co spowoduje wyświetlenie błędu, ale wygeneruje nazwę główną usługi. Następnie zapoznaj się z [tymi krokami](#set-the-right-permissions-for-key-operations) wcześniej w tym artykule.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

   Musisz nadać magazynowi kluczy dostęp do nowego serwera.

4. Po zarejestrowaniu nazwy głównej usługi należy ponownie sprawdzić poprawność klucza, a serwer wznawia jego normalne działanie.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu":::

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta](concepts-data-encryption-mysql.md).
