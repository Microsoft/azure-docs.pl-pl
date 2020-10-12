---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sieć siatkowa sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do pobierania i usuwania zasobów sieciowych Service Fabric siatki.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245864"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Pobierz i Usuń zasoby sieci siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób sieciowy. |
| list | Wyświetla listę wszystkich zasobów sieciowych. |
| pokaż | Pobiera zasób sieciowy o podaną nazwę. |

## <a name="sfctl-mesh-network-delete"></a>Usuń sieć siatkową sfctl
Usuwa zasób sieciowy.

Usuwa zasób sieciowy identyfikowany przez nazwę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa sieci. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-network-list"></a>Lista sieci siatki sfctl
Wyświetla listę wszystkich zasobów sieciowych.

Pobiera informacje o wszystkich zasobach sieciowych w danej grupie zasobów. Informacje obejmują opis i inne właściwości sieci.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-network-show"></a>Pokaż sieć siatkową sfctl
Pobiera zasób sieciowy o podaną nazwę.

Pobiera informacje o zasobie sieciowym o podanej nazwie. Informacje obejmują opis i inne właściwości sieci.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa sieci. |

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
