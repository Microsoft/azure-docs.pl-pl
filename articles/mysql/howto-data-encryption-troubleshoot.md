---
title: Rozwiązywanie problemów z szyfrowaniem danych — Azure Database for MySQL
description: Dowiedz się, jak rozwiązywać problemy z szyfrowaniem danych w Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: 95b5a7650e0990f13149daeed87da8e261ec37e4
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241127"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Rozwiązywanie problemów z szyfrowaniem danych w Azure Database for MySQL

W tym artykule opisano sposób identyfikowania i rozwiązywania typowych problemów, które mogą wystąpić w Azure Database for MySQL w przypadku skonfigurowania szyfrowania danych przy użyciu klucza zarządzanego przez klienta.

## <a name="introduction"></a>Wprowadzenie

Podczas konfigurowania szyfrowania danych w celu używania klucza zarządzanego przez klienta w Azure Key Vault serwery wymagają ciągłego dostępu do klucza. Jeśli serwer utraci dostęp do klucza zarządzanego przez klienta w Azure Key Vault, spowoduje to odmowę wszystkich połączeń, zwrócenie odpowiedniego komunikatu o błędzie i zmianę jego stanu na * **niedostępne** _ w Azure Portal.

Jeśli serwer Azure Database for MySQL nie jest już potrzebny, możesz go usunąć, aby zatrzymać ponoszenia kosztów. Żadne inne akcje na serwerze nie są dozwolone do momentu przywrócenia dostępu do magazynu kluczy i udostępnienia serwera. Nie jest również możliwe Zmiana opcji szyfrowania danych z `Yes` (zarządzane przez klienta) na `No` (zarządzana przez usługę) na niedostępnym serwerze, gdy jest on szyfrowany przy użyciu klucza zarządzanego przez klienta. Należy ponownie sprawdzić poprawność klucza przed ponownym uzyskaniem dostępu do serwera. Ta akcja jest niezbędna do ochrony danych przed nieautoryzowanym dostępem podczas odwoływania uprawnień do klucza zarządzanego przez klienta.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Typowe błędy, które powodują, że serwer stał się niedostępny

Następujące nieprawidłowe konfiguracje powodują większość problemów z szyfrowaniem danych, które używają Azure Key Vault kluczy:

- Magazyn kluczy jest niedostępny lub nie istnieje:
  - Magazyn kluczy został przypadkowo usunięty.
  - Sporadyczny błąd sieci powoduje, że magazyn kluczy jest niedostępny.

- Nie masz uprawnień dostępu do magazynu kluczy lub klucz nie istnieje:
  - Klucz wygasł lub został przypadkowo usunięty lub wyłączony.
  - Tożsamość zarządzana wystąpienia Azure Database for MySQL została przypadkowo usunięta.
  - Zarządzana tożsamość wystąpienia Azure Database for MySQL ma niewystarczające uprawnienia klucza. Na przykład uprawnienia nie obejmują Get, zawijania i depakowania.
  - Uprawnienia do tożsamości zarządzanej do wystąpienia Azure Database for MySQL zostały odwołane lub usunięte.

## <a name="identify-and-resolve-common-errors"></a>Identyfikowanie i rozwiązywanie typowych błędów

### <a name="errors-on-the-key-vault"></a>Błędy w magazynie kluczy

#### <a name="disabled-key-vault"></a>Wyłączony Magazyn kluczy

- `AzureKeyVaultKeyDisabledMessage`
- _ * Wyjaśnienie * *: nie można ukończyć operacji na serwerze, ponieważ klucz Azure Key Vault jest wyłączony.

#### <a name="missing-key-vault-permissions"></a>Brak uprawnień magazynu kluczy

- `AzureKeyVaultMissingPermissionsMessage`
- **Wyjaśnienie** : serwer nie ma wymaganych uprawnień Get, otocz i unotoką do Azure Key Vault. Udziel każdemu brakującemu uprawnienia do nazwy głównej usługi o IDENTYFIKATORze.

### <a name="mitigation"></a>Ograniczanie ryzyka

- Upewnij się, że klucz zarządzany przez klienta znajduje się w magazynie kluczy.
- Zidentyfikuj Magazyn kluczy, a następnie przejdź do magazynu kluczy w Azure Portal.
- Upewnij się, że identyfikator URI klucza identyfikuje klucz, który jest obecny.

## <a name="next-steps"></a>Następne kroki

[Użyj Azure Portal, aby skonfigurować szyfrowanie danych z kluczem zarządzanym przez klienta w Azure Database for MySQL](howto-data-encryption-portal.md)
