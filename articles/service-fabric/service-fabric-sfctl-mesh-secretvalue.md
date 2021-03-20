---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl siatkę secretvalue
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do pobierania i usuwania Service Fabric siatki secretvalue.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245776"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Pobieranie i usuwanie zasobów secretvalue siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną wartość nazwanego zasobu tajnego. |
| list | Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego. |
| pokaż | Wyświetla określoną wartość zasobu tajnego. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl siatki secretvalue usuwanie
Usuwa określoną wartość nazwanego zasobu tajnego.

Usuwa zasób wartości tajnej identyfikowany przez nazwę. Nazwa zasobu jest zazwyczaj wersją skojarzoną z tą wartością. Usuwanie zakończy się niepowodzeniem, jeśli określona wartość jest używana.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-list"></a>Lista secretvalue siatki sfctl
Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego.

Pobiera informacje o wszystkich zasobach wartości tajnych określonego zasobu tajnego. Informacje te obejmują nazwy zasobów wartości tajnych, ale nie wartości rzeczywiste.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl siatki secretvalue Pokaż
Wyświetla określoną wartość zasobu tajnego.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |
| --show-Value | Pokaż rzeczywistą wartość wersji tajnej. |

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
