---
title: Szyfrowanie danych — Azure Portal — dla Azure Database for PostgreSQL — pojedynczy serwer
description: Dowiedz się, jak skonfigurować szyfrowanie danych dla Azure Database for PostgreSQL jednego serwera i zarządzać nimi przy użyciu Azure Portal.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 07e103c3e1f56e8a46ea24e750d83e719abab3d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81457981"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Szyfrowanie danych dla Azure Database for PostgreSQL pojedynczego serwera przy użyciu Azure Portal

Dowiedz się, jak za pomocą Azure Portal skonfigurować szyfrowanie danych dla Azure Database for PostgreSQL jednego serwera i zarządzać nimi.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* W Azure Key Vault Utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta.
* Magazyn kluczy musi mieć następujące właściwości, które mają być używane jako klucz zarządzany przez klienta:
  * [Usuwanie nietrwałe](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Przeczyść chronione](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty do użycia jako klucz zarządzany przez klienta:
  * Brak daty wygaśnięcia
  * Niewyłączone
  * Możliwość wykonywania operacji pobrania, zawijania klucza i rozwinięcia klucza

## <a name="set-the-right-permissions-for-key-operations"></a>Ustaw odpowiednie uprawnienia dla operacji Key

1. W Key Vault wybierz pozycję **zasady dostępu**  >  **Dodaj zasady dostępu**.

   ![Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Wybierz pozycję **uprawnienia**, a następnie wybierz pozycję **Pobierz**, **Zawijaj**, **Odpakuj**oraz **podmiot zabezpieczeń**, który jest nazwą serwera PostgreSQL. Jeśli nie można znaleźć podmiotu zabezpieczeń serwera na liście istniejących podmiotów zabezpieczeń, należy go zarejestrować. Zostanie wyświetlony monit o zarejestrowanie podmiotu zabezpieczeń serwera podczas próby skonfigurowania szyfrowania danych po raz pierwszy i niepowodzenie.  

   ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Wybierz pozycję **Zapisz**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ustawianie szyfrowania danych dla Azure Database for PostgreSQL pojedynczego serwera

1. W obszarze Azure Database for PostgreSQL wybierz pozycję **szyfrowanie danych** , aby skonfigurować klucz zarządzany przez klienta.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL z wyróżnionym szyfrowaniem danych](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Można wybrać Magazyn kluczy i parę kluczy lub wprowadzić identyfikator klucza.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL, z wyróżnionymi opcjami szyfrowania danych](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Wybierz pozycję **Zapisz**.

4. Aby upewnić się, że wszystkie pliki (w tym pliki tymczasowe) są całkowicie zaszyfrowane, należy ponownie uruchomić serwer.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Korzystanie z szyfrowania danych dla serwerów przywracania lub repliki

Po zaszyfrowaniu pojedynczego serwera Azure Database for PostgreSQL z kluczem zarządzanym przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą operacji repliki (lokalnej/obejmującej wiele regionów). W przypadku zaszyfrowanego serwera PostgreSQL można wykonać następujące czynności, aby utworzyć zaszyfrowany przywrócony serwer.

1. Na serwerze wybierz pozycję **Przegląd**  >  **przywracanie**.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL, z wyróżnioną funkcją przegląd i przywracanie](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Lub dla serwera z włączoną replikacją, w obszarze **Ustawienia** wybierz pozycję **replikacja**.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL, z wyróżnioną replikacją](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Po zakończeniu operacji przywracania tworzony nowy serwer jest szyfrowany przy użyciu klucza serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer jest niedostępny. Zapobiega to manipulowaniu danymi, ponieważ dla nowej tożsamości serwera nie udzielono jeszcze uprawnienia dostępu do magazynu kluczy.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL z wyróżnionym stanem niedostępnym](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Aby zapewnić dostęp do serwera, ponownie sprawdź poprawność klucza na przywróconym serwerze. Wybierz pozycję **szyfrowanie danych**ponownie  >  **Sprawdź poprawność klucza**.

   > [!NOTE]
   > Pierwsza próba ponownego zweryfikowania zakończy się niepowodzeniem, ponieważ nazwa główna usługi nowego serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, wybierz pozycję **Sprawdź ponownie klucz**, co spowoduje wyświetlenie błędu, ale wygeneruje nazwę główną usługi. Następnie zapoznaj się z [tymi krokami](#set-the-right-permissions-for-key-operations) wcześniej w tym artykule.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL, z wyróżnionym krokiem ponownej walidacji](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Musisz nadać magazynowi kluczy dostęp do nowego serwera.

4. Po zarejestrowaniu nazwy głównej usługi należy ponownie sprawdzić poprawność klucza, a serwer wznawia jego normalne działanie.

   ![Zrzut ekranu przedstawiający Azure Database for PostgreSQL, pokazujący przywrócone funkcje](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for PostgreSQL szyfrowanie danych na jednym serwerze z kluczem zarządzanym przez klienta](concepts-data-encryption-postgresql.md).
