---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — usługa siatki sfctl — replika
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń pobierania szczegółów repliki dla zasobów aplikacji.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245725"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Pobierz szczegóły repliki i Wyświetl listę replik danej usługi w zasobie aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| list | Wyświetla listę wszystkich replik usługi. |
| pokaż | Pobiera daną replikę usługi aplikacji. |

## <a name="sfctl-mesh-service-replica-list"></a>Usługa siatki sfctl — lista replik
Wyświetla listę wszystkich replik usługi.

Pobiera informacje o wszystkich replikach usługi. Informacje obejmują opis i inne właściwości repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name--Application-Name [wymagane] | Nazwa aplikacji. |
| --Service-Name [wymagane] | Nazwa usługi. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-service-replica-show"></a>Usługa siatki sfctl — Pokaż replikę
Pobiera daną replikę usługi aplikacji.

Pobiera informacje o replice usługi o podanej nazwie. Informacje obejmują opis i inne właściwości repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name--Application-Name [wymagane] | Nazwa aplikacji. |
| --Name-n [wymagane] | Nazwa repliki usługi. |
| --Service-Name [wymagane] | Nazwa usługi. |

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
