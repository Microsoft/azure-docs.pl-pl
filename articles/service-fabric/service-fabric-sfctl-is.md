---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń związanych z zarządzaniem infrastrukturą.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906075"
---
# <a name="sfctl-is"></a>sfctl is
Wykonywanie zapytań i wysyłanie poleceń do usługi infrastruktury.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| command | Wywołuje polecenie administracyjne w danym wystąpieniu usługi infrastruktury. |
| query | Wywołuje zapytanie tylko do odczytu dla danego wystąpienia usługi infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl jest poleceniem
Wywołuje polecenie administracyjne w danym wystąpieniu usługi infrastruktury.

W przypadku klastrów, dla których skonfigurowano co najmniej jedno wystąpienie usługi infrastruktury, ten interfejs API umożliwia wysyłanie poleceń specyficznych dla infrastruktury do określonego wystąpienia usługi infrastruktury. Dostępne polecenia i odpowiadające im formaty odpowiedzi różnią się w zależności od infrastruktury, w której działa klaster. Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --polecenie [wymagane] | Tekst polecenia, który ma zostać wywołany. Zawartość polecenia jest specyficzna dla infrastruktury. |
| --ID usługi | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schematu identyfikatora URI "Sieć szkieletowa \: ". Ten parametr jest wymagany tylko w przypadku klastra, na którym działa więcej niż jedno wystąpienie usługi infrastruktury. |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-is-query"></a>sfctl jest kwerendą
Wywołuje zapytanie tylko do odczytu dla danego wystąpienia usługi infrastruktury.

W przypadku klastrów, dla których skonfigurowano co najmniej jedno wystąpienie usługi infrastruktury, ten interfejs API umożliwia wysyłanie zapytań specyficznych dla infrastruktury do określonego wystąpienia usługi infrastruktury. Dostępne polecenia i odpowiadające im formaty odpowiedzi różnią się w zależności od infrastruktury, w której działa klaster. Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --polecenie [wymagane] | Tekst polecenia, który ma zostać wywołany. Zawartość polecenia jest specyficzna dla infrastruktury. |
| --ID usługi | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schematu identyfikatora URI "Sieć szkieletowa \: ". Ten parametr jest wymagany tylko w przypadku klastra, na którym działa więcej niż jedno wystąpienie usługi infrastruktury. |
| --timeout-t | Wartość domyślna \: 60. |

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
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
