---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: c2548b1669366564809ed2fde725cb3399922a29
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803542"
---
Usługa Azure Key Vault obsługuje dwa typy zasobów: magazyny i zarządzane sprzętowych modułów zabezpieczeń. W poniższych dwóch sekcjach opisano odpowiednio limity usługi dla każdego z nich.

### <a name="resource-type-vault"></a>Typ zasobu: Magazyn

W tej sekcji opisano limity usługi dla typu zasobu `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Najważniejsze transakcje (maksymalna liczba transakcji dozwolonych w ciągu 10 sekund, na magazyn na region<sup>1</sup>):

|Typ klucza|Klucz HSM<br>Utwórz klucz|Klucz HSM<br>Wszystkie inne transakcje|Klucz oprogramowania<br>Utwórz klucz|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2 048-bit|5|1000|10|2000|
|RSA 3 072-bit|5|250|10|500|
|RSA 4 096-bit|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|SECP256K1 ECC|5|1000|10|2000|
||||||

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

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Wpisy tajne, zarządzane klucze konta magazynu i transakcje magazynu:

| Typ transakcji | Maksymalna dozwolona liczba transakcji w ciągu 10 sekund, na magazyn na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |

Aby uzyskać informacje na temat sposobu obsługi ograniczania w przypadku przekroczenia limitów, zobacz [Azure Key Vault wskazówki dotyczące ograniczania przepustowości](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> limit całej subskrypcji dla wszystkich typów transakcji wynosi pięć razy na limit magazynu kluczy. Na przykład usługi HSM — inne transakcje na subskrypcję są ograniczone do 5 000 transakcji w ciągu 10 sekund na subskrypcję.

#### <a name="backup-keys-secrets-certificates"></a>Klucze kopii zapasowych, wpisy tajne, certyfikaty

Podczas tworzenia kopii zapasowej obiektu magazynu kluczy, takiego jak klucz tajny, klucz lub certyfikat, operacja tworzenia kopii zapasowej pobiera obiekt jako zaszyfrowany obiekt BLOB. Nie można odszyfrować tego obiektu blob poza platformą Azure. Aby uzyskać użyteczne dane z tego obiektu BLOB, należy przywrócić obiekt BLOB do magazynu kluczy w ramach tej samej subskrypcji platformy Azure i lokalizacji geograficznej platformy Azure

| Typ transakcji | Dozwolone maksymalne wersje obiektów magazynu kluczy |
| --- | --- |
| Tworzenie kopii zapasowej pojedynczego klucza, wpisu tajnego, certfiicate |500 |

> [!NOTE]
> Próba utworzenia kopii zapasowej obiektu Key, Secret lub Certificate z większą liczbą wersji niż powyżej spowoduje wystąpienie błędu. Nie można usunąć poprzednich wersji klucza, wpisu tajnego ani certyfikatu. 

#### <a name="azure-private-link-integration"></a>Integracja z prywatnym łączem platformy Azure

> [!NOTE]
> Liczba magazynów kluczy z włączonymi prywatnymi punktami końcowymi na subskrypcję jest przystosowanym limitem. Limit przedstawiony poniżej jest domyślnym limitem. Jeśli chcesz poprosić o zwiększenie limitu dla usługi, Wyślij wiadomość e-mail na adres akv-privatelink@microsoft.com . Te żądania będą zatwierdzane w przypadku poszczególnych przypadków.

| Zasób | Limit |
| -------- | -----:|
| Prywatne punkty końcowe dla magazynu kluczy | 64 |
| Magazyny kluczy z prywatnymi punktami końcowymi na subskrypcję | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Typ zasobu: zarządzany moduł HSM (wersja zapoznawcza)

W tej sekcji opisano limity usługi dla typu zasobu `managed HSM` .

#### <a name="object-limits"></a>Limity obiektów

|Element|Limity|
|----|------:|
Liczba wystąpień modułu HSM na subskrypcję na region|1 (w trakcie okresu zapoznawczego)
Liczba kluczy na pulę HSM|5000
Liczba wersji na klucz|100
Liczba niestandardowych definicji ról dla modułu HSM|50
Liczba przypisań ról w zakresie HSM|50
Liczba przypisań ról w każdym indywidualnym zakresie klucza|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limity transakcji dla operacji administracyjnych (liczba operacji na sekundę na wystąpienie modułu HSM)
|Operacja |Liczba operacji na sekundę|
|----|------:|
Wszystkie operacje RBAC<br/>(obejmuje wszystkie operacje CRUD dla definicji ról i przypisań ról)|5
Pełna kopia zapasowa/przywracanie modułu HSM<br/>(obsługiwana jest tylko jedna współbieżna operacja tworzenia kopii zapasowej lub przywracania na wystąpienie modułu HSM)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limity transakcji dla operacji kryptograficznych (liczba operacji na sekundę na wystąpienie modułu HSM)

- Każde zarządzane wystąpienie modułu HSM stanowi 3 partycje HSM o zrównoważonym obciążeniu. Limity przepływności to funkcja odpowiedniej pojemności sprzętowej przydzielonej dla każdej partycji. W poniższych tabelach przedstawiono maksymalną przepływność z użyciem co najmniej jednej partycji. Rzeczywista przepływność może być większa niż 3.
- Zanotowane limity przepływności zakładają, że jeden pojedynczy klucz jest używany do osiągnięcia maksymalnej przepływności. Na przykład jeśli jest używany pojedynczy klucz RSA-2048, maksymalna przepływność będzie wynosić 1100 operacji podpisywania. Jeśli używasz 1100 różnych kluczy z 1 transakcję na sekundę, nie będą one w stanie osiągnąć tej samej przepływności.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operacje na klucz RSA (liczba operacji na sekundę na wystąpienie modułu HSM)

|Operacja|2048 — bit|3072 — bit|4096 — bit|
|--|--:|--:|--:|
Utwórz klucz|1| 1| 1
Usuń klucz (usuwanie nietrwałe)|10|10|10 
Wyczyść klucz|10|10|10 
Klucz kopii zapasowej|10|10|10 
Przywróć klucz|10|10|10 
Pobierz informacje o kluczu|1100|1100|1100
Szyfrowanie|10 000|10 000|6000
Odszyfrowywanie|1100|360|160
Zawijanie|10 000|10 000|6000
Unwrap|1100|360|160
Znak|1100|360|160
Weryfikacja|10 000|10 000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operacje na kluczu EC (liczba operacji na sekundę na wystąpienie modułu HSM)

W tej tabeli opisano liczbę operacji na sekundę dla każdego typu krzywej.

|Operacja|P-256|P-256 K|P-384|P-521|
|---|---:|---:|---:|---:|
Utwórz klucz| 1| 1| 1| 1
Usuń klucz (usuwanie nietrwałe)|10|10|10|10
Wyczyść klucz|10|10|10|10
Klucz kopii zapasowej|10|10|10|10
Przywróć klucz|10|10|10|10
Pobierz informacje o kluczu|1100|1100|1100|1100
Znak|260|260|165|56
Weryfikacja|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operacje na klucz AES (liczba operacji na sekundę na wystąpienie modułu HSM)
- Operacje szyfrowania i odszyfrowywania założono, że rozmiar pakietu 4 KB.
- Limity przepływności szyfrowania/odszyfrowywania dotyczą algorytmów AES-CBC i AES-GCM.
- Limity przepływności dla otoki/rozwinięcia stosują się do algorytmu AES-KW.

|Operacja|128 — bit|192 — bit|256 — bit|
|--|--:|--:|--:|
Utwórz klucz|1| 1| 1
Usuń klucz (usuwanie nietrwałe)|10|10|10
Wyczyść klucz|10|10|10
Klucz kopii zapasowej|10|10|10
Przywróć klucz|10|10|10
Pobierz informacje o kluczu|1100|1100|1100
Szyfrowanie|8000|8000 |8000 
Odszyfrowywanie|8000|8000|8000
Zawijanie|9000|9000|9000
Unwrap|9000|9000|9000

