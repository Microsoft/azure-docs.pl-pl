---
title: Jak zapewnić sprawdzanie poprawności szyfrowania danych Azure Database for PostgreSQL
description: Dowiedz się, jak zweryfikować szyfrowanie szyfrowania danych Azure Database for PostgreSQL przy użyciu klucza zarządzanego przez klientów.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 7ec27cc4f28151214ca97ffb5113607d6b60ee36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240583"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Weryfikowanie szyfrowania danych dla Azure Database for PostgreSQL

Ten artykuł pomaga sprawdzić, czy szyfrowanie danych przy użyciu klucza zarządzanego przez klienta Azure Database for PostgreSQL działa zgodnie z oczekiwaniami.

## <a name="check-the-encryption-status"></a>Sprawdź stan szyfrowania

### <a name="from-portal"></a>Z poziomu portalu

1. Jeśli chcesz sprawdzić, czy klucz klienta jest używany do szyfrowania, wykonaj następujące czynności:

    * W Azure Portal przejdź do   ->  **kluczy** Azure Key Vault
    * Wybierz klucz używany do szyfrowania serwera.
    * Ustaw stan klucza z **włączony** na wartość **nie**.
  
       Po pewnym czasie (**~ 15 min**) **stan** serwera Azure Database for PostgreSQL powinien być **niedostępny**. Wszystkie operacje we/wy wykonywane względem serwera zakończą się niepowodzeniem, co oznacza, że serwer jest rzeczywiście szyfrowany za pomocą klucza Customers, a klucz jest obecnie nieprawidłowy.
    
        Aby zapewnić **dostęp** do serwera, możesz ponownie sprawdzić poprawność klucza. 
    
    * Ustaw stan klucza w Key Vault na **tak**.
    * Na stronie **szyfrowanie danych** serwera wybierz pozycję ponownie **Zweryfikuj klucz**.
    * Po pomyślnym zakończeniu ponownej weryfikacji klucza **stan** serwera zmieni się na **dostępne**

2. Na Azure Portal, jeśli masz pewność, że klucz szyfrowania jest ustawiony, dane są szyfrowane przy użyciu klucza klienci używanego w Azure Portal.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Przegląd zasad dostępu":::

### <a name="from-cli"></a>Z interfejsu wiersza polecenia

1. Możemy użyć polecenia *AZ CLI* polecenie, aby sprawdzić poprawność najważniejszych zasobów używanych dla serwera Azure Database for PostgreSQL.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    W przypadku serwera bez zestawu szyfrowania danych to polecenie spowoduje wyświetlenie pustego zestawu [].

### <a name="azure-audit-reports"></a>Raporty inspekcji platformy Azure

Można również przejrzeć [raporty inspekcji](https://servicetrust.microsoft.com) , które zawierają informacje o zgodności z normami ochrony danych i wymaganiami prawnymi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for PostgreSQL szyfrowanie danych na jednym serwerze z kluczem zarządzanym przez klienta](concepts-data-encryption-postgresql.md).