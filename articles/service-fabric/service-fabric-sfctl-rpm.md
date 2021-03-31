---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl obr./min
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń dla usługi Repair Manager.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7317fd66303aaabf5232106aa7391439880bebaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260286"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Wykonywanie zapytań i wysyłanie poleceń do usługi Repair Manager.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Zatwierdź — Wymuś | Wymusza zatwierdzenie danego zadania naprawy. |
| delete | Usuwa zakończone zadanie naprawy. |
| list | Pobiera listę zadań naprawczych zgodnych z podanym filtrem. |

## <a name="sfctl-rpm-approve-force"></a>sfctl RPM — Zatwierdź — Wymuś
Wymusza zatwierdzenie danego zadania naprawy.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Task-ID [wymagane] | Identyfikator zadania naprawy. |
| --Version | Bieżący numer wersji zadania naprawy. Jeśli wartość jest różna od zera, żądanie zakończy się powodzeniem tylko wtedy, gdy zostanie ona zgodna z rzeczywistą bieżącą wersją zadania naprawy. Jeśli wartość jest równa zero, Sprawdzanie wersji nie jest wykonywane. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-delete"></a>sfctl RPM
Usuwa zakończone zadanie naprawy.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Task-ID [wymagane] | Identyfikator ukończonego zadania naprawy, które ma zostać usunięte. |
| --Version | Bieżący numer wersji zadania naprawy. Jeśli wartość jest różna od zera, żądanie zakończy się powodzeniem tylko wtedy, gdy zostanie ona zgodna z rzeczywistą bieżącą wersją zadania naprawy. Jeśli wartość jest równa zero, Sprawdzanie wersji nie jest wykonywane. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-rpm-list"></a>Lista sfctl RPM
Pobiera listę zadań naprawczych zgodnych z podanym filtrem.

Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --wykonawcze-Filter | Nazwa programu wykonującego naprawy, którego odpowiednie zadania powinny znajdować się na liście. |
| --State-Filter | Bitowe lub następujące wartości określające, które Stany zadań mają być uwzględnione na liście wyników. <ul><li>1 — utworzono</li><li>2 — zatwierdzono</li><li>4 — przygotowywanie</li><li>8 — zatwierdzone</li><li>16 — wykonywanie</li><li>32 — przywracanie</li><li>64 — ukończono</li></ul>
| --Task-ID-Filter | Prefiks identyfikatora zadania naprawy, który ma zostać dopasowany. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](./scripts/sfctl-upgrade-application.md).
