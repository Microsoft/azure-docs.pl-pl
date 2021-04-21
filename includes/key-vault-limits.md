---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753306"
---
Azure Key Vault obsługuje dwa typy zasobów: magazyny i zarządzane moduły HSM. W poniższych dwóch sekcjach opisano limity usług dla każdej z nich.

### <a name="resource-type-vault"></a>Typ zasobu: magazyn

W tej sekcji opisano limity usługi dla typu zasobu `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transakcje kluczy (maksymalna liczba transakcji dozwolonych w ciągu 10 sekund, na magazyn na region<sup>1):</sup>

|Typ klucza|Klucz HSM<br>CREATE key|Klucz HSM<br>Wszystkie inne transakcje|Klucz oprogramowania<br>CREATE key|Klucz oprogramowania<br>Wszystkie inne transakcje|
|:---|---:|---:|---:|---:|
|RSA 2048-bitowy|5|1000|10|2000|
|RSA 3072-bitowy|5|250|10|500|
|RSA 4096-bitowy|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
||||||

> [!NOTE]
> W poprzedniej tabeli widzimy, że w przypadku 2048-bitowych kluczy oprogramowania RSA dozwolone jest 2000 transakcji GET na 10 sekund. W przypadku 2048-bitowych kluczy HSM RSA dozwolone jest 1000 transakcji GET na 10 sekund.
>
> Progi ograniczania są ważone, a wymuszanie jest na ich sumie. Na przykład, jak pokazano w poprzedniej tabeli, w przypadku wykonywania operacji GET na kluczach HSM RSA użycie kluczy 4096-bitowych jest osiem razy droższe niż w przypadku kluczy 2048-bitowych. Wynika to z tego, że 1000/125 = 8.
>
> W danym 10-sekundowym interwale klient  Azure Key Vault może wykonać tylko jedną z następujących operacji, zanim napotka kod stanu HTTP ograniczania `429` przepustowości:
> - 2000 transakcji GET klucza oprogramowania 2000 RSA 2048-bitowych
> - 1000 transakcji GET 2048-bitowego klucza HSM RSA
> - 125 RSA 4096-bitowe transakcje GET klucza HSM
> - 124 RSA 4096-bitowe transakcje GET klucza HSM i 8 transakcji GET 2048-bitowego klucza HSM RSA

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Wpisy tajne, klucze konta magazynu zarządzanego i transakcje magazynu:

| Typ transakcji | Maksymalna dozwolona liczba transakcji w ciągu 10 sekund na magazyn na region<sup>1</sup> |
| --- | --- |
| Wszystkie transakcje |2000 |

Aby uzyskać informacje na temat sposobu obsługi ograniczania przepustowości w przypadku przekroczenia tych limitów, [zobacz Azure Key Vault wskazówki dotyczące ograniczania przepustowości.](../articles/key-vault/general/overview-throttling.md)

<sup>1</sup> Limit dla wszystkich typów transakcji dla całej subskrypcji wynosi pięć razy na limit magazynu kluczy. Na przykład inne transakcje modułu HSM na subskrypcję są ograniczone do 5000 transakcji w ciągu 10 sekund na subskrypcję.

#### <a name="backup-keys-secrets-certificates"></a>Klucze kopii zapasowych, wpisy tajne, certyfikaty

Podczas tworzenia kopii zapasowej obiektu magazynu kluczy, takiego jak klucz tajny, klucz lub certyfikat, operacja tworzenia kopii zapasowej pobierze obiekt jako zaszyfrowany obiekt blob. Tego obiektu blob nie można odszyfrować poza platformą Azure. Aby uzyskać użyteczne dane z tego obiektu blob, musisz przywrócić obiekt blob do magazynu kluczy w ramach tej samej subskrypcji platformy Azure i lokalizacji geograficznej platformy Azure

| Typ transakcji | Dozwolona maksymalna liczba dozwolonych wersji obiektów magazynu kluczy |
| --- | --- |
| Tworzenie kopii zapasowej pojedynczego klucza, klucza tajnego, certyfikatu |500 |

> [!NOTE]
> Próba tworzenia kopii zapasowej obiektu klucza, klucza tajnego lub certyfikatu z większą niż powyżej wartością limitu spowoduje błąd. Nie można usunąć poprzednich wersji klucza, klucza tajnego ani certyfikatu. 

#### <a name="azure-private-link-integration"></a>Azure Private Link integracji

> [!NOTE]
> Liczba magazynów kluczy z włączonymi prywatnymi punktami końcowymi na subskrypcję to dostosowywany limit. Przedstawiony poniżej limit jest limitem domyślnym. Jeśli chcesz poprosić o zwiększenie limitu dla swojej usługi, utwórz wniosek o pomoc techniczną i zostanie on oceniony dla każdego przypadku.

| Zasób | Limit |
| -------- | -----:|
| Prywatne punkty końcowe na magazyn kluczy | 64 |
| Magazyny kluczy z prywatnymi punktami końcowymi na subskrypcję | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Typ zasobu: zarządzany moduł HSM (wersja zapoznawcza)

W tej sekcji opisano limity usługi dla typu zasobu `managed HSM` .

#### <a name="object-limits"></a>Limity obiektów

|Element|Limity|
|----|------:|
Liczba wystąpień modułu HSM na subskrypcję na region|1 (w wersji zapoznawczej)
Liczba kluczy na pulę modułów HSM|5000
Liczba wersji na klucz|100
Liczba niestandardowych definicji ról na moduł HSM|50
Liczba przypisań ról w zakresie modułu HSM|50
Liczba przypisań ról w każdym zakresie poszczególnych kluczy|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limity transakcji dla operacji administracyjnych (liczba operacji na sekundę na wystąpienie modułu HSM)
|Operacja |Liczba operacji na sekundę|
|----|------:|
Wszystkie operacje RBAC<br/>(obejmuje wszystkie operacje CRUD dla definicji ról i przypisań ról)|5
Pełna kopia zapasowa/przywracanie modułu HSM<br/>(obsługiwana jest tylko jedna współbieżna operacja tworzenia kopii zapasowej lub przywracania na wystąpienie modułu HSM)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limity transakcji dla operacji kryptograficznych (liczba operacji na sekundę na wystąpienie modułu HSM)

- Każde wystąpienie zarządzanego modułu HSM składa się z 3 partycji modułu HSM ze zrównoważonym obciążeniem. Limity przepływności są funkcją podstawowej pojemności sprzętowej przydzielonej dla każdej partycji. W poniższych tabelach przedstawiono maksymalną przepływność z co najmniej jedną dostępną partycją. Rzeczywista przepływność może być nawet 3 razy większa, jeśli są dostępne wszystkie 3 partycje.
- W przypadku zanotowanej wartości limitów przepływności przyjęto założenie, że jeden klucz jest używany do osiągnięcia maksymalnej przepływności. Jeśli na przykład jest używany pojedynczy klucz RSA-2048, maksymalna przepływność będzie 1100 operacji podpisywania. Jeśli używasz 1100 różnych kluczy z jedną transakcją na sekundę, nie będzie można osiągnąć tej samej przepływności.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operacje na kluczach RSA (liczba operacji na sekundę na wystąpienie modułu HSM)

|Operacja|2048-bitowy|3072-bitowy|4096-bitowy|
|--|--:|--:|--:|
Tworzenie klucza|1| 1| 1
Usuń klucz (usuwanie nie soft-delete)|10|10|10 
Klucz przeczyszczania|10|10|10 
Klucz kopii zapasowej|10|10|10 
Przywróć klucz|10|10|10 
Uzyskiwanie kluczowych informacji|1100|1100|1100
Szyfrowanie|10 000|10 000|6000
Odszyfrowywanie|1100|360|160
Zawijanie|10 000|10 000|6000
Unwrap|1100|360|160
Znak|1100|360|160
Weryfikacja|10 000|10 000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operacje klucza EC (liczba operacji na sekundę na wystąpienie modułu HSM)

W tej tabeli opisano liczbę operacji na sekundę dla każdego typu krzywej.

|Operacja|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Tworzenie klucza| 1| 1| 1| 1
Usuń klucz (usuwanie nie soft-delete)|10|10|10|10
Klucz przeczyszczania|10|10|10|10
Klucz kopii zapasowej|10|10|10|10
Przywróć klucz|10|10|10|10
Uzyskiwanie kluczowych informacji|1100|1100|1100|1100
Znak|260|260|165|56
Weryfikacja|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operacje klucza AES (liczba operacji na sekundę na wystąpienie modułu HSM)
- Operacje szyfrowania i odszyfrowywania mają rozmiar pakietu 4 KB.
- Limity przepływności szyfrowania/odszyfrowywania dotyczą algorytmów AES-CBC i AES-GCM.
- Limity przepływności dla opakuj/odpakuj mają zastosowanie do algorytmu AES-KW.

|Operacja|128-bitowy|192-bitowy|256-bitowy|
|--|--:|--:|--:|
Tworzenie klucza|1| 1| 1
Usuń klucz (usuwanie nie soft-delete)|10|10|10
Klucz przeczyszczania|10|10|10
Klucz kopii zapasowej|10|10|10
Klucz przywracania|10|10|10
Uzyskiwanie kluczowych informacji|1100|1100|1100
Szyfrowanie|8000|8000 |8000 
Odszyfrowywanie|8000|8000|8000
Zawijanie|9000|9000|9000
Unwrap|9000|9000|9000

