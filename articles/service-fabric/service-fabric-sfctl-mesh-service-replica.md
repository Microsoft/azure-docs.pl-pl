---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — usługa siatki sfctl — replika
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń pobierania szczegółów repliki dla zasobów aplikacji.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905939"
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
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).