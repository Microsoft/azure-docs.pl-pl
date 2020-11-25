---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: e4abbeadb0d30911d99fff57c0e99a3e427a6d8d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027640"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Najważniejsze transakcje (maksymalna liczba transakcji dozwolonych w ciągu 10 sekund, na magazyn na region<sup>1</sup>):

|Typ klucza|Klucz HSM<br>Utwórz klucz|Klucz HSM<br>Wszystkie inne transakcje|Klucz oprogramowania<br>Utwórz klucz|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2 048-bit|5|1000|10|2000|
|RSA 3 072-bit|5|250|10|500|
|RSA 4 096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|SECP256K1 ECC|5|1000|10|2000|

> [!NOTE]
> W poprzedniej tabeli widzimy, że dla 2 000 kluczy oprogramowania RSA 2 048-bitowe są dozwolone opłaty za 10 sekund. W przypadku RSA 2 048-bitowe klucze HSM, 1 000 pobieranie transakcji na 10 sekund jest dozwolone.
>
> Wartości progowe ograniczania przepustowości są ważone i wymuszanie jest w ich sumie. Na przykład, jak pokazano w poprzedniej tabeli, po wykonaniu operacji GET na kluczach HSM RSA — jest to osiem razy droższe do używania 4 096-bitowych kluczy w porównaniu z kluczami bitowymi 2 048. Dzieje się tak, ponieważ 1000/125 = 8.
>
> W danym przedziale 10 sekund klient Azure Key Vault może wykonać *tylko jedną* z następujących operacji przed wystąpieniem `429` kodu stanu HTTP ograniczenia przepustowości:
> - 2 000 RSA 2 048-bitowe oprogramowanie — transakcje pobierania kluczy
> - 1 000 RSA 2 048-bitowego modułu HSM — UZYSKIWANie transakcji klucza
> - 125 RSA 4 096-bitowego modułu HSM — UZYSKIWANie transakcji klucza
> - 124 RSA 4 096-bitowy moduł HSM-Key GET Transactions i 8 RSA 2 048-bitowego modułu HSM — transakcje pobierania kluczy

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Wpisy tajne, zarządzane klucze konta magazynu i transakcje magazynu:

| Typ transakcji | Maksymalna dozwolona liczba transakcji w ciągu 10 sekund, na magazyn na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |

Aby uzyskać informacje na temat sposobu obsługi ograniczania w przypadku przekroczenia limitów, zobacz [Azure Key Vault wskazówki dotyczące ograniczania przepustowości](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> limit całej subskrypcji dla wszystkich typów transakcji wynosi pięć razy na limit magazynu kluczy. Na przykład usługi HSM — inne transakcje na subskrypcję są ograniczone do 5 000 transakcji w ciągu 10 sekund na subskrypcję.

### <a name="azure-private-link-integration"></a>Integracja z prywatnym łączem platformy Azure

> [!NOTE]
> Liczba magazynów kluczy z włączonymi prywatnymi punktami końcowymi na subskrypcję jest przystosowanym limitem. Limit przedstawiony poniżej jest domyślnym limitem. Jeśli chcesz poprosić o zwiększenie limitu dla usługi, Wyślij wiadomość e-mail na adres akv-privatelink@microsoft.com . Te żądania będą zatwierdzane w przypadku poszczególnych przypadków.

| Zasób | Limit |
| -------- | ----- |
| Prywatne punkty końcowe dla magazynu kluczy | 64 |
| Magazyny kluczy z prywatnymi punktami końcowymi na subskrypcję | 400 |