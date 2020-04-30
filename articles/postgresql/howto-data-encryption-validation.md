---
title: Jak zapewnić sprawdzanie poprawności szyfrowania danych Azure Database for PostgreSQL
description: Dowiedz się, jak zweryfikować szyfrowanie szyfrowania danych Azure Database for PostgreSQL przy użyciu klucza zarządzanego przez klientów.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 4672a92ceba5dc52c717f76a705d0fa508ab41fd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515471"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Weryfikowanie szyfrowania danych dla Azure Database for PostgreSQL

Ten artykuł pomaga sprawdzić, czy szyfrowanie danych przy użyciu klucza zarządzanego przez klienta Azure Database for PostgreSQL działa zgodnie z oczekiwaniami.

## <a name="check-the-encryption-status"></a>Sprawdź stan szyfrowania

### <a name="from-portal"></a>Z portalu

1. Jeśli chcesz sprawdzić, czy klucz klienta jest używany do szyfrowania, wykonaj następujące czynności:

    * W Azure Portal przejdź do**kluczy** **Azure Key Vault** -> 
    * Wybierz klucz używany do szyfrowania serwera.
    * Ustaw stan klucza z **włączony** na wartość **nie**.
  
       Po pewnym czasie (**~ 15 min**) **stan** serwera Azure Database for PostgreSQL powinien być **niedostępny**. Wszystkie operacje we/wy wykonywane względem serwera zakończą się niepowodzeniem, co oznacza, że serwer jest rzeczywiście szyfrowany za pomocą klucza Customers, a klucz jest obecnie nieprawidłowy.
    
        Aby zapewnić **dostęp** do serwera, możesz ponownie sprawdzić poprawność klucza. 
    
    * Ustaw stan klucza w Key Vault na **tak**.
    * Na stronie **szyfrowanie danych**serwera wybierz pozycję ponownie **Zweryfikuj klucz**.
    * Po pomyślnym zakończeniu ponownej weryfikacji klucza **stan** serwera zmieni się na **dostępne**

2. Na Azure Portal, jeśli masz pewność, że klucz szyfrowania jest ustawiony, dane są szyfrowane przy użyciu klucza klienci używanego w Azure Portal.

  ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

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