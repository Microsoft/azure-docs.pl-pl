---
title: Szyfrowanie danych — witryna Azure portal — usługa Azure Database for MySQL
description: Dowiedz się, jak skonfigurować szyfrowanie danych dla usługi Azure Database dla mysql i zarządzać nimi przy użyciu witryny Azure portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9d1e89919647d9d94b287618da2f9a77278425a5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459087"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Szyfrowanie danych dla usługi Azure Database dla MySQL przy użyciu witryny Azure portal

Dowiedz się, jak skonfigurować szyfrowanie danych dla usługi Azure Database dla mysql za pomocą portalu Azure portal i zarządzać nimi.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem w tej subskrypcji.
* W usłudze Azure Key Vault utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta.
* Magazyn kluczy musi mieć następujące właściwości, aby użyć go jako klucza zarządzanego przez klienta:
  * [Usuwanie nietrwałe](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Czyszczenie chronione](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty, aby użyć go jako klucza zarządzanego przez klienta:
  * Brak daty ważności
  * Nie wyłączono
  * W stanie wykonać get, wrap key, rozpakować kluczowe operacje

## <a name="set-the-right-permissions-for-key-operations"></a>Ustawianie odpowiednich uprawnień dla operacji kluczy

1. W obszarze Przechowalnia kluczy wybierz pozycję **Zasady** > dostępu**Dodaj zasady dostępu**.

   ![Zrzut ekranu przedstawiający przechowalnię kluczy z wyróżnioną pojwale zasad programu Access i dodaj zasady dostępu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Wybierz **opcję Uprawnienia klucza**i wybierz pozycję **Pobierz**, **Zawiń**, **Rozpakuj**i **główny podmiot ,** który jest nazwą serwera MySQL. Jeśli podmiotu zabezpieczeń serwera nie można znaleźć na liście istniejących podmiotów, należy go zarejestrować. Podczas pierwszej próby skonfigurowania szyfrowania danych po raz pierwszy zostanie wyświetlony monit o zarejestrowanie podmiotu zabezpieczeń serwera, co nie powiedzie się.

   ![Omówienie zasad dostępu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Wybierz pozycję **Zapisz**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ustawianie szyfrowania danych dla usługi Azure Database dla mysql

1. W usłudze Azure Database for MySQL wybierz **opcję Szyfrowanie danych,** aby skonfigurować klucz zarządzany przez klienta.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z wyróżnioną pozycją szyfrowania danych](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Można wybrać przechowalnię kluczy i parę kluczy lub wprowadzić identyfikator klucza.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z wyróżnionymi opcjami szyfrowania danych](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Wybierz pozycję **Zapisz**.

4. Aby upewnić się, że wszystkie pliki (w tym pliki tymczasowe) są w pełni zaszyfrowane, uruchom ponownie serwer.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Używanie szyfrowania danych do przywracania lub repliki serwerów

Po zaszyfrowywanie usługi Azure Database for MySQL przy pomocą klucza zarządzanego klienta przechowywanego w usłudze Key Vault każda nowo utworzona kopia serwera jest również szyfrowana. Tę nową kopię można wykonać za pomocą operacji lokalnego lub przywracania geograficznego lub za pośrednictwem operacji repliki (lokalnej/międzyregionowej). Tak więc dla zaszyfrowanego serwera MySQL, można użyć następujących kroków, aby utworzyć zaszyfrowany serwer przywrócony.

1. Na serwerze wybierz pozycję **Przegląd** > **przywracania**.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z wyróżnioną pozycją Przegląd i przywracanie](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Lub dla serwera obsługującego replikację w nagłówku **Ustawienia** wybierz pozycję **Replikacja**.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z wyróżnioną wyróżnioną funkcją Replikacja](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Po zakończeniu operacji przywracania nowy utworzony serwer jest szyfrowany przy pomocą klucza serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer jest niedostępny. Zapobiega to manipulowaniu danymi, ponieważ tożsamość nowego serwera nie uzyskała jeszcze uprawnień dostępu do magazynu kluczy.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z wyróżnionym stanem niedostępnym](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Aby udostępnić serwer, ponownie zatrwalić klucz na przywróconym serwerze. Wybierz**klucz Ponowne przetwarzanie** **szyfrowania** > danych .

   > [!NOTE]
   > Pierwsza próba ponownego oceny nie powiedzie się, ponieważ nowy podmiot usługi serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, wybierz **klucz ponownej oceny,** który wyświetli błąd, ale generuje jednostkę usługi. Następnie zapoznaj się z [tymi krokami](#set-the-right-permissions-for-key-operations) wcześniej w tym artykule.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z wyróżnionym krokiem ponownego unieważnienia](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Będziesz musiał dać dostęp do magazynu kluczy do nowego serwera.

4. Po zarejestrowaniu jednostki usługi ponownie przywtniu certyfikat klucza, a serwer wznawia jego normalne funkcje.

   ![Zrzut ekranu przedstawiający usługę Azure Database for MySQL z przywróconą funkcjonalnością](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej o szyfrowaniu danych, zobacz [Usługa Azure Database for MySQL data encryption with customer-managed key](concepts-data-encryption-mysql.md).
